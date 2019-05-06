# ADPluginTemplate

This repository contains a pre-organized file structure for writing areaDetector plugins.
To use it, start by downloading a zip of one of the releases. You can use wget:
```
wget https://github.com/epicsNSLS2-areaDetector/ADPluginTemplate/archive/R0.3.zip
```
OR
```
wget https://github.com/epicsNSLS2-areaDetector/ADPluginTemplate/archive/R0.3.tar.gz
```
if you prefer tar over zip.

**DO NOT CLONE THIS REPOSITORY WHEN WRITING A NEW PLUGIN**

If you do, the git information will be cloned as well, and version control for your plugin will
contain all changes made to this template.

Once you download and the template, unzip and navigate to the scripts directory as follows:
```
unzip R0.3.zip
cd ADPluginTemplate-R0.3
cd scripts
```
inside, you will find three scripts:

Script | Description | Usage
--------|------------------------|--------------
update_name.py | A python script that updates occurances of the plugin name | python3 update_name.py -n $NAME
add_pv.py | A python script that adds boilerplate code for new PVs to the plugin | python3 add_pv.py -n $PV_NAME -t $PV_TYPE -d $PV_DTYPE
initializePlugin.sh | A bash script that runs the python script and inits git version control | ./initializePlugin.sh $NAME

To fully automate the initial setup, you may run the `initializePlugin.sh` script (requires bash > 4.0). This will run the `update_name.py` script, and initialize a git repo, and make an initial commit. You will need to add a remote repository in order to be able to push the git changes however, and a global git config is required for the git commands to function correctly.

Alternatively, you may run the `update_name.py` script by itself, which will update all occurances of the plugin name in the template to a name of your choosing.

Also, make sure to replace this README.md file with one that explains how to use your new plugin!

### Using add_pv.py

The add_pv.py script is meant to simplify writing the plugin after the template has been installed. When adding a PV to an asynPortDriver (NDPluginDriver extends this class, and as a result every Area Detector plugin is a type of asynPortDriver), you need to make several changes:

* You must add the PV record definition to the EPICS database .template file
* You must define a string variable in the driver source code that links to the asyn port
* You must define an integer variable that will act as the index to the PV value in the driver
* You must make a call to createParam() in the driver constructor to initialize the PV
* Also, if the PV is the first or last PV, you must set the DRIVER_FIRST_PV or DRIVER_LAST_PV variables to the index of each respective PV. (this is so the driver knows which range of PV indexes are driver specific)

The add_pv.py script is meant to greatly simplify this process by allowing a script to automatically add all of the necessary boilerplate code for a new PV. To use the script do the following in order:
1) start by deciding on a PV string name, which is an all-caps name for the PV, with underscores for spaces. Example: `EXPOSURE_TIME` would be the PV String name for an exposure time PV.
2) Next, decide on the PV data type. You can choose between Int32, Float64, and Octet. Ex: for exposure, we need decimal values, so we would choose Float64. Octet is used for strings.
3) Finally, decide on the PV type. These are: binary, multibit, analog, string, waveform. Ex. Exposure time is an analog value, a dropdown pv menu would be multi bit, a message would be a string or waveform.

Now, simply run the script, specifying your choices by the appropriate flags:

Flag   | Usage
-------|--------
-n     | PV String name (`EXPOSURE_TIME`)
-f      | If this PV is the first added to the project, add this -f flag
-d      | The data type for the PV (Int32, Float64, Octet)
-t      | PV type (binary, multibit, analog, string, waveform)

An example run of the script to add exposure time, operating mode, and status message PVs would be:
```
python3 add_pv.py -n EXPOSURE_TIME -f -d Float64 -t analog
python3 add_pv.py -n OPERATION_MODE -d Int32 -t multibit
python3 add_pv.py -n STATUS_MESSAGE -d Octet -t waveform
```
