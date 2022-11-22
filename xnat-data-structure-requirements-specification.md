# XNAT Data Structure 

## Abstract 

...

**Goal**: give a structured description and insight in how non-raw DICOM and non-DICOM data (e.g., converted DICOM, segmentations, derived data) in a project is structured, what the files contain, and allow users to expose this structure through the API to be used with tooling like [xnatpy](https://xnat.rtfd.io). 

 
### Introduction: Structure File Concept 

Describes how non-raw DICOM data is organized in your project and what the files mean/contain. 
* Project resource: `STRUCTURE/definitions_{timestamp/version?}.yaml`  
* Use *xnatpy* to expose all resources within a project through an API 

 

### Roles: 
* Data user: for instance, a researcher consuming scan data to derive [Quantitative Imaging Biomarkers](https://en.wikipedia.org/wiki/Imaging_biomarker)
* Data controller: for instance, a clinical research PI having a data collection to share with others 

 

## Requirements 

### Product requirements 

As a data user I want: 
* To find the T1w scan because I want to extract information from it 
* To know where and how to store the output of my image analysis workflow so I and others can find it later 
* To know what information is stored in this file “ambigousfile.extension” because it might contain relevant information for my research 
* To know how many patients have a T1w scan with a fully annotated brain segmentation by observer Radiologist 1 to extract information from it 
* To know by who/when/how a “file.extension” was uploaded 
* To explore what kind of segmentation files are available for a certain scan/patient 
* To know whether a certain scan session is pre-operative or post-operative 
* To easily be able to upload a folder structure with derived files to my project  

As a data controller I want: 
* Define the structure of my data collection, so it is easy to share it with others 
* Define the structure of my data collection, so I can collect summary statistics or aggregates to export to a data catalog in robust, automatic fashion 
* Others to be able to understand the data in my project without me having to explain everything 
* To know if the data collections adhere to the defined data structures and as such are valid, because I need my repository to be adhering to the FAIR principles 
* To semantically annotate my scans (e.g. DICOM series) and expose this to the users or a catalogue to inform them what a scan contains 

Technical requirements :
* The structure will be defined in a yaml or json file 
* Optionally: a json schema definition need to be implemented to do validity checking of the data structure yaml/json file 
* The structure file needs to be human readable whilst also machine parsable for being able to be used in data structure validity checking or data catalogue exporting, etc. 

     

### Non-goals / out-of-scope 
* Provenance tracking (although provenance potentially benefits from documenting your data structure)

## Assumptions 
* It is assumed you store your data on XNAT 
* At first, we foresee to rely on xnatpy for keeping the structure definition and the actual stored files in sync. This could be moved to xnat features like the event and container service. 

     
## Solution Mock-up

[Link to a working demo with WORC database](https://gitlab.com/radiology/infrastructure/xnatpy/-/tree/feature/data-structure-demo/xnat/tests)

*Uploading/retrieving derived data (simplest form)*: 

```python
# upload 
scan.brain_mask = "/path/to/local_file.nii.gz"

# download 
scan.brain_mask.download("/path/to/local_file.nii.gz")
```

*Slightly more complex use case where for example we have a scan, but in it there is also derived data of the scan in some atlas space, but also in the original scan space*: 

```python
# download 
scan.atlas.mask.brain.download("/path/to/local_file.nii.gz") 
scan.original.mask.brain.download("/path/to_loca_file.nii.gz") 

# What data is available in atlas space?
scan.atlas.list_folders()
# or
scan.atlas.list_files() 
```
 
*Finding specific scans/scan sessions*:
```python
# Get all experiments with the label "preoperative" for a certain subject 
preoperative_experiments = connection.projects['worc'].subjects['CRLM-002'].experiments["preoperative"] 
```

## Idea and wish list
This is a random list of ideas and wishes that need working out a but more
* Automatic population of structure file  
* Currently manually filled in by user 
* Connect with forms.io and container service 
* Automatic population of structure file 
* Validity checking (event service?) 
* Is the data there? 
* Datatypes: is the data not corrupt / in the right format? 
* Schema’s? 
* Hierarchy 
* Links to existing ontologies and data models (e.g., BIDS, SNOMED-CT)
* `structure.print()`: immediate overview 

 
