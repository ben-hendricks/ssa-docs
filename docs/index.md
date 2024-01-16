# Overview

Hydra (Hydrophone Data Reduction and Analysis) is a modular Python software for the detection, classification, and localization of bio-acoustic signals in ocean acoustic recordings. It was built to run alongside active recording efforts, providing fully automated ocean intelligence in near real-time. It has been developed by [SoundSpace Analytics](https://www.soundspaceanalytics.ca).

Hydra is written in Python3 and consists of python scripts executed through a terminal window as well as a user interface to help executing and monitoring the software.

Hydra receives acoustic recordings stored on a computer or accessible external drive, and returns .csv files with signal detection and localization statistics.

Three bio-acoustic processing modules : a signal detector, a signal classifier, and a signal localizer.
Each element builds on each other. This means the localizer requires the output of the signal classifier, which requires the output of the signal detector. At the same time, the modules are independent entities and if the classifier or localizer is activated without the proper input feed from the previous module, it will idle until that data is provided.