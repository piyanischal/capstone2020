# Return_result

a POST request is invoked by the client to the server. It returns a JSON file with information like client_id, job, data, and jobs.

## JSON definition

A job is communicated as a simple JSON object where key names and values are lower case:

* `client_id`: A unique identifier of the client returning the result.
* `job_id`: The id of job being returned.
* `data`: A list of JSON objects. Each object in the list contains folder name, file name, and data
  * `folder_name`: The path to the location of where the data should be placed. Check data storage section to see the hierarchy of the path.
  * `file_name`: Name of the file to which the data will be stored. Named after the type of data it is storing.
  * `data`: The raw JSON data received from querying regulations.gov.
* `jobs`: A list of new job objects. These job objects omit the job_id field. Jobs are retrieved through the JSON data collected from the regulations server.

See `job.md` for specification of each job.

## Data Storage Structure

```
Data (folder)
  Agency (folder)
    DocketID (folder)
      docket.json
      DocumentID (folder)
        basic_document.json
        document.json
```     
* For example, Data can be considered the root

```
EPA/EPA-HQ-OAR-2011-0028/EPA-HQ-OAR-2011-0028-0108/document.json

Agency: EPA
DocketID: EPA-HQ-OAR-2011-0028
DocumentID: EPA-HQ-OAR-2011-0028-0108
File: document.json
```


## Examples of Result Objects

* Docket
  * Returns data concerning a docket
  * Placed as a json file in the DocketID folder
  * Does not contain a `jobs` field
```
{
  'client_id': 'client14',
  'job_id': 'job33',
  'data': [
    {
      'folder_name': 'EPA/EPA-HQ-OAR-2011-0028/'
      'file_name': 'docket.json'
      'data': {
        'agency': 'EPA',
        'docket_id': 'EPA-HQ-OAR-2011-0028'
        'file_contents' = {
          "agency": "Environmental Protection Agency",
          "agencyAcronym": "EPA",
          "cfrCitation": "40 CFR 98",
        
          ...

          "internationalImpacts": {
            "label": "International Impacts",
            "value": "No"
          }
        }
      }
    }
  ]
}
```

* Document
  * Returns data concerning a document
  * Placed as a json file in the DocumentID folder
  * `jobs` field can contain a list of download jobs
```
{
  'client_id': 'client23',
  'job_id': 'job42',
  'data': [
    {
      'folder_name': 'EPA/EPA-HQ-OAR-2011-0028/EPA-HQ-OAR-2011-0028-0108/'
      'file_name': 'document.json'
      'data': {
        'agency': 'EPA',
        'docket_id': 'EPA-HQ-OAR-2011-0028'
        'document_id': 'EPA-HQ-OAR-2011-0028-0108'
        'file_contents' = {
          "allowLateComment": false,
          "commentDueDate": null,
          "effectiveDate": "2014-01-01T00:00:00-05:00",
          
          ...

          "numItemsRecieved": {
          "label": "Number of Comments Received",
          "value": "0"
          },
          "agencyAcronym": {
            "label": "Agency",
            "value": "EPA"
          }
        }
      }
    }
  ] 
  'jobs': [
    {
      'job_type': 'download',
      'url': 'https://api.data.gov/regulations/v3/download?documentId=EPA-HQ-OAR-2011-0028-0108&contentType=pdf'
    },
    {
      'job_type': 'download',
      'url':  'https://api.data.gov/regulations/v3/download?documentId=EPA-HQ-OAR-2011-0028-0108&contentType=html'
    }
  ]
}
```

* Documents
  * Returns basic data concerning a document
  * Placed as a json file in the DocumentID folder
  * `jobs` field contains a list of document and docket jobs
```
{
  'client_id': 'client1',
  'job_id': 'job1`,
  'data': [
    {
      'folder_name': 'CMS/CMS-2005-0001/CMS-2005-0001-0001/'
      'file_name': 'basic_document.json'
      'data': {
        "agencyAcronym": "CMS",
        "allowLateComment": false,
        "attachmentCount": 0,
        "commentDueDate": "2005-10-03T23:59:59-04:00",
        "commentStartDate": "2005-08-04T00:00:00-04:00",
        "docketId": "CMS-2005-0001",
        "docketTitle": "Medicare Program; Revised Civil Money Penalties, Assessments, Exclusions, and Related Appeals Procedures",
        "docketType": "Nonrulemaking",
        "documentId": "CMS-2005-0001-0001",
        "documentStatus": "Posted",
        "documentType": "Proposed Rule",
        "frNumber": "05-15291",
        "numberOfCommentsReceived": 0,
        "openForComment": false,
        "postedDate": "2005-08-04T00:00:00-04:00",
        "title": "Medicare Program; Revised Civil Money Penalties, Assessments, Exclusions, and Related Appeals Procedures"
      }
    },
    
              ...

    {
      'folder_name': 'FMCSA/FMCSA-1997-2350/FMCSA-1997-2350-21655/'
      'file_name': 'basic_document.json'
      'data': {
        "agencyAcronym": "FMCSA",
        "allowLateComment": false,
        "attachmentCount": 1,
        "commentDueDate": "2000-12-15T23:59:59-05:00",
        "commentStartDate": null,
        "docketId": "FMCSA-1997-2350",
        "docketTitle": "Notice of Proposed Rulemaking (NPRM) - Hours of Service of Drivers",
        "docketType": "Rulemaking",
        "documentId": "FMCSA-1997-2350-21655",
        "documentStatus": "Posted",
        "documentType": "Public Submission",
        "numberOfCommentsReceived": 1,
        "openForComment": false,
        "postedDate": "2000-10-17T00:00:00-04:00",
        "rin": "2126-AA23",
        "title": "Lee & Jo Batton - Comments"
      }
    }
  ]
  }
  'jobs': [
    {
      'job_type': 'docket'
      'docket_id': 'EPA-HQ-OAR-2011-0028'
    },
    
    ...

    {
      'job_type': 'document',
      'document_id': 'EPA-HQ-OAR-2011-0028-0108'
    }
  ]
}
```
