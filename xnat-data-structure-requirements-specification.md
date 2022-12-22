# XNAT Data Structure 

## Abstract 

...

**Goal**: give a structured description and insight in how non-raw DICOM
and non-DICOM data (e.g., converted DICOM, segmentations, derived data) 
in a project is structured, what the files contain, and allow users to
expose this structure through the API to be used with tooling like 
[xnatpy](https://xnat.rtfd.io). 

## Motivation

The XNAT medical image storage solution has defined a data structure for
medical imaging data. This is following the concepts/levels of `project`,
`subject`, `experiment`, `scan`, and `resource`. This gives the data its
general structure. The names of each level and the contents are not
pre-defined. This allows flexibility for users to store any kind of data in
the archive and match their use-case, but also makes the data hard to interpret
without additional information. Getting access to a project on XNAT will not
make you understand the data further than the general structure.

For data to be FAIR the data should be well described so that new users can
understand and use the data. This requires a more comprehensive description of
the data in an archive. For this we propose to create a *data-structure file*
for each project on XNAT, which describes what data is available for that 
project.

Such a file will not only allow people to immediately know what data is in a 
project, but also allow the creation of tools to help with data-management. 
These tools could include tools to validate data, select and process data, 
storing data under the right place automatically and more.

## Impact

The choice for a data-structure file to improve the data structure in XNAT is
inspired by limiting the negative impact on users, while allowing for the 
advantages via opt-in.

* This proposal will only add optional functionality and not hamper existing
  users in any way. They will only be affected if they opt in to use the
  data-structure file.
* Projects with a data-structure file will have:
  * their data described in a way that is both human and machine-readable
  * be able to use any tools implementing this definition
  * be (more) FAIR

### General  planning

The development of the XNAT data structure file project will be in an on 
open fashion. The aim is to use a test-driven development approach, where
we first defined requirements, then the desired interface, then create tests, 
and finally implement code to satisfy all defined requirements and tests.

The following steps are identified and tentative deadlines
are set:

1. Requirements engineering until 2023-02-01
2. Draft data-structure file specification until 2023-03-01
3. Define xnatpy data-access interface and tests cases until 2023-05-01
4. Implement data-access interface until 2023-06-01


 
### Introduction: Structure File Concept 

Describes how non-raw DICOM data is organized in your project and what the 
files mean/contain. 

* Project resource: `STRUCTURE/definitions_{timestamp/version?}.yaml`  
* Use *xnatpy* to expose all resources within a project through an API 

 

### Roles: 
* Data user: for instance, a researcher consuming scan data to derive
* [Quantitative Imaging Biomarkers](https://en.wikipedia.org/wiki/Imaging_biomarker)
* Data controller: for instance, a clinical research PI having a data
* collection to share with others 

 

## Requirements 

### Product requirements 

As a data user I want: 
* To find the T1w scan because I want to extract information from it 
* To know where and how to store the output of my image analysis workflow, 
  so I and others can find it later 
* To know what information is stored in this file “ambiguousfile.extension” 
  because it might contain relevant information for my research 
* To know how many patients have a T1w scan with a fully annotated brain 
  segmentation by observer Radiologist 1 to extract information from it 
* To know by who/when/how a “file.extension” was uploaded 
* To explore what kind of segmentation files are available for a certain 
  scan/patient 
* To know whether a certain scan session is pre-operative or post-operative 
* To easily be able to upload a folder structure with derived files to my project  

As a data controller I want: 
* Define the structure of my data collection, so it is easy to share it
  with others 
* Define the structure of my data collection, so I can collect summary 
  statistics or aggregates to export to a data catalog in robust, automatic fashion 
* Others to be able to understand the data in my project without me having to
  explain everything 
* To know if the data collections adhere to the defined data structures and as
  such are valid, because I need my repository to be adhering to the FAIR principles 
* To semantically annotate my scans (e.g. DICOM series) and expose this to
  the users or a catalogue to inform them what a scan contains 

Technical requirements :
* The structure will be defined in a yaml or json file 
* Optionally: a json schema definition need to be implemented to do validity
  checking of the data structure yaml/json file 
* The structure file needs to be human readable whilst also machine parsable 
  for being able to be used in data structure validity checking or data 
  catalogue exporting, etc. 

     

### Non-goals / out-of-scope 
* Provenance tracking (although provenance potentially benefits from documenting
  your data structure)

## Assumptions 
* It is assumed you store your data on XNAT 
* At first, we foresee to rely on xnatpy for keeping the structure definition and
  the actual stored files in sync. This could be moved to xnat features like the event and container service. 

     
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

 
