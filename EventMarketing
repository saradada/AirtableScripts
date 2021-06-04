//This script generates Marketing Asset requests for each speaker, sponsor, and session for an event. 
//It ensures that all content and copy have been approved before a request is generated.
//It also checks for duplicates in case the script is run multiple times.

// @ts-nocheck
// pick tables from your base here
let socialTable = base.getTable('Custom Social');
let speakerTable = base.getTable('Speakers');
let sponsorTable = base.getTable('Sponsors');

//define fields here
let speakerName = speakerTable.getField("Full Name");
let speakerApproved = speakerTable.getField("Copy Approved?");
let socialType = socialTable.getField("Type");
let socialSpeaker = socialTable.getField("Speaker");
let socialSponsor = socialTable.getField("Sponsor");
let sponsorName = sponsorTable.getField("Company");
let sponsorApproved = sponsorTable.getField("Copy Approved?");
let sessionApproved = speakerTable.getField("Copy Approved?");

//Load all records in Speaker Table
let result = await speakerTable.selectRecordsAsync();
let sponsorResult = await sponsorTable.selectRecordsAsync();

//Load all records in Social table
let socialResult = await socialTable.selectRecordsAsync();

//
// create hash index
//
async function createIndex(tableName, fieldName)
{
    let thisTable    = base.getTable(tableName);
    let queryResults = await thisTable.selectRecordsAsync();
    let oIndex       = {};
    for (let record of queryResults.records)
    {
        oIndex[record.getCellValue(fieldName)] = record.id;
    }
    return(oIndex);
}

var oLookupTableIndex = await createIndex("Sponsors", "Company");

var bLookupTableIndex = await createIndex("Speakers", "Full Name");

//FOR LOOP checking each record
for(let record of result.records) {
    let originalValue = record.getCellValue(speakerApproved);
    let sessionVal = record.getCellValue(sessionApproved);
    var duplicate = 0;
    //IF check is approved
    if(originalValue == true && sessionVal == 1) {
        duplicate++
        //THEN check duplicate
        for (let socialRecord of socialResult.records) {
            let socialVal = socialRecord.getCellValueAsString("Speaker");
            if (socialVal == record.getCellValue(speakerName))
            {
                duplicate++;
            }
        }
        if (duplicate ==1) {
            await socialTable.createRecordAsync({
            'Speaker': [ { "id" : bLookupTableIndex[record.getCellValue('Full Name')] } ], // get 'Full name'
            'Type': "Speaker" // get 'Session Title'
        })
        // full name goes to speaker/sponsor
        // type gets set to speaker
        // session title -> type/title
        }    
    }
}

for(let row of sponsorResult.records) {
    let sponsCheck = row.getCellValue(sponsorApproved);
    var duplicateCheck = 0;
    //IF check is approved
    if(sponsCheck == true) {
        duplicateCheck++
        //THEN check duplicate
        for (let socialRec of socialResult.records) {
            let socialVal1 = socialRec.getCellValueAsString("Sponsor");
            if (socialVal1 == row.getCellValue(sponsorName))
            {
                duplicateCheck++;
            }
        }
        if (duplicateCheck ==1) {
            await socialTable.createRecordAsync({
            'Sponsor': [ { "id" : oLookupTableIndex[row.getCellValue('Company')] } ], // get 'Full name'
            'Type': "Sponsor", // get 'Session Title'
        })
        // full name goes to speaker/sponsor
        // type gets set to speaker
        // session title -> type/title
        }    
    }
}
