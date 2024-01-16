# Running Hydra

Currently, Hydra consists of three bio-acoustic processing modules: a signal detector, a signal classifier, and a signal localizer.
Each element builds on each other. This means the localizer requires the output of the signal classifier, which requires the output of the signal detector. At the same time, the modules are independent of each other in that all three can run on their own if the others are de-activated. Therefore, if the classifier or localizer is activated without the proper input feed from the previous module, it will simply idle until that data is provided.

The source code for each module is organized the following way:
- ==core_modules== contain the basic functionality and algorithms that do not require configuration and are only edited for development purposes.
- ==front_modules== handle the execution of the modules. It contains a ==params.py==, ==func.py==, and ==exec.py== file for each module. The *params* files configure the algorithms themselves and should only be edited when you know what you are doing. The *func*, and *exec* files handle the execution of the module and are edit-for-development only. 
	- ==make_tdoa_model.py== designs the localization setup and the spatial configuration of hydrophones as well as other parameters such as the sound speed profile that is relevant for computing localization models. This file needs to be configured for each new localization setup, and requires editing if, e.g., a hydrophone is re-located or a change in the sound speed profile is found.
	- ==fname_interpreter.py== configures the execution for a set of data on a specific machine. This files needs to be configured before each run of Hydra.


## Configuration

Configuration of Hydra is achieved through a single file: ==fname_interpreter.py==, located in ==front_modules==.

1. Filename nomenclature

	Hydra must extract the timestamp from each acoustic file, assuming this to be the start time of the recording. For this, edit those elements in fname_itp_dateime to indicate the string section of the appropriate timestamp information within the filename:

	```python
	file_datestr = os.path.basename(filename)[15:30] # EDIT THIS
	```

	as well as the timestamp encoding of the string section:


	```python
	datestr_layout = '%Y%m%dT%H%M%S' # EDIT THIS
	```

2. Logging directory and interval

	Hydra will produce multiple log files: dlog, plog, and clog (see fname_interperter.py for details). The root directory for all log files needs to be defined seperately. The default should always be. 

	All log files will be created and stored in subdirectories according to the logging interval which can be chosen from daily to annually. The default logging interval is monthly.  

	??? example

		```python
		RootDir_dlog = /path/to/hydra/data/dlog
		RootDir_plog = /path/to/hydra/data/plog
		RootDir_clog = /path/to/hydra/data/clog

		```


3. Verbose

	Hydra communicates back to the user in three ways.

	a. Feedback is given through the Terminal; this option provides the most detailed feedback and can be used for development and troubleshooting the algorithms.
	
	b. Feedback is given through the UI; this option provides core feedback to the UI; this option is the default and should be used during normal operation.
	
	c. No feedback is given; this option is the fastest but will give no indication about system health or processing status; this option should only be used if a sucessfull run can be savely assumed and no monitoring is required or expected.



4. Processing Interval

	Three paramters define the time window Hydra considered for processing. The ==lookback== parameter defines a time (in seconds) Hydra will look in the past for files to process. The ==lookto== and ==lookfrom== parameters define a time window within which acoustic files are processed. To achive maximum flexibility, the three parameters work as an `AND` logic: all time conditions must be met for a file to be processed.

	???+ note

		The lookback window is relative to the local computer time on which Hydra is executed compared to the timestamp of acoustic data. Make sure to account for the fact that those may not operate in the same timezone.


5. Acoustic file properties

	Properties of the acoustic recordings to be processed are defined in ==rec_dict==. It is a key-value dictionary supplying information about samplingrate, file type (wav/FLAC), length of recording, timezone difference between computer and data timestamps, as well as information whether or not files from multiple hydrophone elements are synchronized. 
	The latter is required for Hydra's localization module. Synchronized data means that all hydrophone units involved in localization are stored in identical intervals and thus will have identical timestamps encoded in their filenames.

	???+ note

		Synchronized filenames from multiple hydrophone elements is now a requirement for localization. Hydra will not attempt to match files with arbitrary recording intervals.


	??? example

		```python
		rec_dict = {
				'FS' : 64000,
				'DTYPE' : '.flac',
				'RECLEN' : 300.0,
				'UTCTOLOC': -7.0,
				'SYNCHED' : True
				}
		```


6. Hydrophone Unit Properties

	Properties of the hydrophone unit(s) are defined in ==h_dict==. It is a key-value dictorionary supplying information about ownership, the deployment site, and the unit itself. It also provides the root directory for the acoustic data associated with the unit as well as a template for acceptable filenames to be considered for processing.

	???+ note

		It is prudent to define the filename template as concise as possible to avoide accidental processing of undesired files.


	6.a. Setup ^^without^^ localization

	The key for only a single hydrophone is supplied. The *ROLE* key must be set to 'master'.

	??? example

		```python
		h_dict = {

		'ICHF1280' : {	'OWNER' : 'SWAG',
					'POPNAME' : 'OtterChannel',
					'SITE_ID' : 'OTTR-01',
					'H_ID' : 'ICHF1280',
					'FNAME_H_ID' : '1280',
					'LAT' : '+59.2393',
					'LON' : '-128.35574',
					'X_UTM': 466701.,
					'Y_UTM': 5895834.9,
					'FNAME_TEMPLATE' : 'ICLISTENHF1280_2023????T??????.???Z_2023????T??????.???Z.flac',
					'ROOTDIR' : '/Volumes/SWAG-20220520-PR/OTTR-01/ICHF1280-20221016/',
					'ROLE' : 'master' 
					}
		```

	6.b. Setup ^^with^^ localization

	The keys for all hydrophones associated in the localization process are supplied. The *ROLE* key must be set to 'master' for the unit that is executed through the Hydra UI. All other units only serve as cross correlation partners and their *ROLE* must be set to 'slave'.

	???+ note

		Hydrophone units assigned a 'slave' role will not be processed and no data products will be produced for them. 


	??? example

		```python
		h_dict = {
		'ICHF1280' : {	'OWNER' : 'SWAG',
						'POPNAME' : 'OtterChannel',
						'SITE_ID' : 'OTTR-01',
						'H_ID' : 'ICHF1280',
						'FNAME_H_ID' : '1280',
						'LAT' : '',
						'LON' : '',
						'X_UTM': 466701.,
						'Y_UTM': 5895834.9,
						'FNAME_TEMPLATE' : 'ICLISTENHF1280_2023????T??????.???Z_2023????T??????.???Z.flac',
						'ROOTDIR' : '/Volumes/SWAG-20220520-PR/OTTR-01/ICHF1280-20221016/',
						'ROLE' : 'slave' 
						},

		'ICHF6033' : {	'OWNER' : 'SWAG',
						'POPNAME' : 'CampaniaIsland',
						'SITE_ID' : 'CAMP-01',
						'H_ID' : 'ICHF6033',
						'FNAME_H_ID' : '6033',
						'LAT' : '',
						'LON' : '',
						'X_UTM': 466701.,
						'Y_UTM': 5895834.9,
						'FNAME_TEMPLATE' : 'ICLISTENHF6033_2023????T??????.???Z_2023????T??????.???Z.flac',
						'ROOTDIR' : '/Volumes/SWAG-20220520-PR/CAMP-01/ICHF6033-20221016/',
						'ROLE' : 'slave' 
						},

		'ICHF6155' : {	'OWNER' : 'SWAG',
						'POPNAME' : 'FinIsland',
						'SITE_ID' : 'FNIS-01',
						'H_ID' : 'ICHF6155',
						'FNAME_H_ID' : '6155',
						'LAT' : '',
						'LON' : '',
						'X_UTM': 466701.,
						'Y_UTM': 5895834.9,
						'FNAME_TEMPLATE' : 'ICLISTENHF6155_2023????T??????.???Z_2023????T??????.???Z.flac',
						'ROOTDIR' : '/Volumes/SWAG-20220520-PR/FNIS-01/ICHF6155-20220521/',
						'ROLE' : 'master' 
						},

		'ICHF6154' : {	'OWNER' : 'SWAG',
						'POPNAME' : 'GilIsland',
						'SITE_ID' : 'GLIS-01',
						'H_ID' : 'ICHF6154',
						'FNAME_H_ID' : '6154',
						'LAT' : '',
						'LON' : '',
						'X_UTM': 466701.,
						'Y_UTM': 5895834.9,
						'FNAME_TEMPLATE' : 'ICLISTENHF6154_2023????T??????.???Z_2023????T??????.???Z.flac',
						'ROOTDIR' : '/Volumes/SWAG-20220520-PR/GLIS-01/ICHF6154-20220521/',
						'ROLE' : 'slave' 
						}
		}
		```



## The UI

The UI has been developed as a bare-bone interface to facilitate execution/termination of all bio-acoustic processing routines and as an easy way for users to monitor progress and system status. Most of the configuration parameters that were previously defined in fname_interpreter.py are displayed in the UI for confirmation.

The Hydra UI is opened through the terminal. 

```sh
conda activate SSP3
python path/to/Hydra*.py
```


<figure markdown>
  ![Hydra UI 2023](/assets/Hydra_UI.png)
  <figcaption>Figure 1: Hydra User Interface, 2023 Version.</figcaption>
</figure>

### Run Hydra

Each module is selected through the Module Sidebar and activated using the Activate Button. To pause the processing for a module, press the Stop Button.   

### Monitor Progress

Progress is monitored in the Status section. It provides information about the processing progress for each module. The status bar indicates the fraction of detected files that have been sucessfully processed. Below, information is given about the total number of processed files ("Files"), the percentage towards completion ("Progress"), as well as the estimated time to completion ("Time"). The bottom field always displays the last file that has been processed, unless the module as been paused ("idle"), or is in the process of gathering data after being activated ("initiated").

The Summary section gives a count of all call detections/localizations made. It is available only for the classification and localization module.

The Status and Summary sections are updated with each processed file.


### Terminate the UI

The UI is closed by simply closing the window it is executed in. The program will always ask for a confirmation of this action and will stop all active processes when confirmation is given. 

