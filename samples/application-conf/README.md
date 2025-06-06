# Application Configuration
This folder contains application specific configuration properties used by the zAppBuild Groovy build and utility scripts. It is intended to be copied as a high level folder in the application repository or main application repository if the application source files are distributed across multiple repositories. Once copied to the application repository, users should review the default property files and modify any values as needed.

At the beginning of the build, the `application.properties` file will automatically be searched and loaded if it exists into the [DBB BuildProperties class](https://www.ibm.com/docs/en/dbb/2.0?topic=apis-build-properties#build-properties-class). The `application.properties` file is by default searched in the `application-conf` folder of the application, but this can be configured through the `applicationConfDir` property in [build-conf/build.properties](../../build-conf/build.properties). Use the `applicationPropFiles` property (see table below) to load additional application property files.

Properties can be overwritten on a per file basis through DBB Build Properties file properties. The tables below indicate which properties keys can be overwritten. It is recommended to manage these overwrites in `file.properties`.

## Property File Descriptions
Since all properties will be loaded into a single static instance of BuildProperties, the organization and naming convention of the *property files* are somewhat arbitrary and targeted more for self documentation and understanding. Properties related to a language script are prefixed with the name of the language script (i.e `cobol_compileParms`).

### application.properties
This property file is loaded automatically at the beginning of the build and contains application specific properties used mainly by `build.groovy` but can also be a place to declare properties used by multiple language scripts. Additional property files are loaded based on the content of the `applicationPropFiles` property.

Property | Description | Overridable
--- | --- | ---
runzTests | Boolean value to specify if zUnit tests should be run.  Defaults to `false`, to enable zUnit Tests, set value to `true`. | false
tazunittest_convertTazResultsToJunit | Boolean value to specify if TAZ Unit Test results need to be converted to JUnit format.  Defaults to `false`, to enable JUnit conversion, set value to `true`. Xalan needs to be available in the users path to help in conversion. | false 
applicationPropFiles | Comma separated list of additional application property files to load. Supports both absolute and relative file paths.  Relative paths assumed to be relative to ${workspace}/${application}/application-conf/. | false
applicationSrcDirs | Define a comma separated list of all source directories included in application build. Each directory is assumed to be a local Git repository clone or subdirectory of it. Supports both absolute and relative paths though for maximum reuse of collected dependency data relative paths should be used. Relative paths assumed to be relative to ${workspace}. | false
skipStoringLogicalFile | Define files that should not be tracked/recorded as logical file in the DBB metadatastore. Files can still be processed by language scripts and will be present in the dbb build reports. | false
excludeFileList | Files that are completely excluded from being processed by the build. | false
skipImpactCalculationList | Files for which the impact analysis should be skipped in impact build | false
buildOrder | Comma separated list of the build script processing order. | false
formatConsoleOutput | Flag to log output in table views instead of printing raw JSON data | false
mainBuildBranch | The main build branch of the main application repository.  Used for cloning collections for topic branch builds instead of rescanning the entire application. | false
gitRepositoryURL | git repository URL of the application repository to establish links to the changed files in the build result properties | false
addSubmodulesToBuildList  | Flag to include Static Sub module files in pipeline builds | false
jobCard | JOBCARD for JCL execs | false
**Build Property management** | |
loadFileLevelProperties | Flag to enable the zAppBuild capability to load individual artifact properties files for a build file | true
loadLanguageConfigurationProperties | Flag to enable the zAppBuild capability to load language configuration properties for build files mapped in languageConfigurationMapping.properties | true
propertyFilePath | relative path to folder containing individual artifact properties files | true
propertyFileExtension | file extension for individual artifact properties files | true
**Dependency and Impact resolution configuration** ||
resolveSubsystems | boolean flag to configure the SearchPathDependencyResolver to evaluate if resolved dependencies impact the file flags isCICS, isSQL, isDLI, isMQ when creating the LogicalFile | false
impactSearch | A list of searchPath configurations to configure the DBB SearchPathImpactFinder API for impact analysis based on dependency information stored in the DBB Metadatastore. | true
dependency resolution rules (DBB search path) | `copybookSearch` used to locate copybooks in dependency resolution process and find impacted programs during impact analysis <br> `pliincludeSearch` used to resolve include files in dependency resolution process and find impacted programs during impact analysis <br> `asmMacroSearch` and `asmCopySearch` used to resolve assembler dependencies in dependency resolution process and find impacted programs during impact analysis `cppHeaderSearch` used to locate c header files in dependency resolution process and to find impacted programs during impact analysis <br> `rexxCopySearch`  used to resolve include files in dependency resolution process and find impacted programs during impact analysis <br> `tazRecordingFileSearch`  used to locate recording files during dependency resolution <br> `eztMacSearch` used to locate ezt macros in dependency resolution process and find impacted programs during impact analysis <br> `bmsSearch` used to find impacted programs during impact analysis <br> `linkSearch` used to find impacted programs during impact analysis <br> | false

### file.properties

Location of file properties, script mappings and file-level property overrides. All file properties for the entire application, including source files in distributed repositories of the application need to be contained either in this file or in other property files in the `application-conf` directory. Look for the column 'Overridable' in the tables below for build properties that can have file-level property overrides. Please also read the section [Build properties](https://www.ibm.com/docs/en/dbb/2.0?topic=apis-build-properties) in the official DBB documentation.

Additional file-level properties can be defined through **individual artifact properties files** in a separate directory of the repository or through **language configuration** files to configure the language scripts. For more details, see [File Property Management](https://github.com/IBM/dbb-zappbuild/docs/FilePropertyManagement.md).

Property | Description
--- | ---
dbb.scriptMapping | DBB configuration file properties association build files to language scripts
dbb.scannerMapping | zAppBuild configuration override/expansion to map files extensions to DBB dependency scanner configurations
cobol_testcase | File property to indicate a generated zUnit cobol test case to use a different set of source and output libraries

Property | Description
--- | ---
loadFileLevelProperties | Flag to enable the zAppBuild capability to load individual artifact properties files for source files. See default configuration in [application.properties](#application.properties)
loadLanguageConfigurationProperties | Flag to enable the zAppBuild capability to load language configuration properties files for source files. See default configuration in [application.properties](#application.properties)
languageConfiguration | Properties mapping for assigning build files to language configuration to define the build configuration

General file level overwrites to control the allocations of system datasets for compile and link steps or activation of preprocessing

Property | Description
--- | ---
isSQL | File property overwrite to indicate that a file requires to include SQL preprocessing, and allocation of Db2 libraries for compile and link phase.
isCICS | File property overwrite to indicate that a file requires to include CICS preprocessing, and allocation of CICS libraries for compile and link phase. Also used to indicate if a *batch* module is executed under CICS for pulling appropriate language interface modules for Db2 or MQ.
isMQ | File property overwrite to indicate that a file requires to include MQ libraries for compile and link phase.
isDLI | File property overwrite to indicate that a file requires to include DLI
isIMS | File property flag to indicate IMS batch and online programs to allocate the IMS RESLIB library during link phase (Compared to the other 4 above flags, the isIMS flag is a pure file property, and not computed by the DBB scanners).

Please note that the above file property settings `isCICS` and `isIMS` are also used to control the allocations when processing link cards with `LinkEdit.groovy` to include the appropriate runtime specific language interfaces.

### reports.properties
Properties used by the build framework to generate reports. Sample properties file to all application-conf to overwrite central build-conf configuration.

Property | Description
--- | ---
reportExternalImpacts | Flag to indicate if an *impactBuild* should analyze and report external impacted files in other collections
reportExternalImpactsAnalysisDepths | Configuration of the analysis depths when performing impact analysis for external impacts (simple|deep)
reportExternalImpactsAnalysisFileFilter | Comma-separated list of pathMatcher filters to limit the analysis of external impacts to a subset of the changed files
reportExternalImpactsCollectionPatterns | Comma-separated list of regex patterns of DBB collection names for which external impacts should be documented
reportConcurrentChanges | Flag to indicate if the build generates reports of concurrent changes to understand recent changes in concurrent configurations (branches)
reportConcurrentChangesGitBranchReferencePatterns | Comma-seperated list of regex patterns defining the branches for which concurrent changes should be calculated
reportConcurrentChangesIntersectionFailsBuild | Flag to indicated if the build is marked as error, when build list intersects with changes on concurrent configurations

### Assembler.properties
Application properties used by zAppBuild/language/Assembler.groovy

Property | Description | Overridable
--- | --- | ---
assembler_fileBuildRank | Default Assemble program build rank. Used to sort Assembler build file sub-list. Leave empty. | true
assembler_pgmParms | Default Assembler parameters. | true
assembler_linkEditParms | Default parameters for the link edit step. | true
assembler_debugParms | Assembler options when the debug flag is set. | true
assembler_compileErrorPrefixParms | Default parameters to support remote error feedback in user build scenarios | true
assembler_eqalangxParms | Default parameters for eqalangx utility to produce debug sidefile. | true
assembler_db2precompilerParms | Default Assembler parameters for Db2 precompiler step. | true
assembler_cicsprecompilerParms | Default Assembler parameters for CICS precompiler step. | true
assembler_asmaOptFile | Optional ASMAOPT file - dataset(member). | true
assembler_linkEdit | Flag indicating to execute the link edit step to produce a load module for the source file.  If false then a object deck will be created instead for later linking. | true
assembler_linkEditStream | Optional linkEditStream defining additional link instructions via SYSIN dd | true
assembler_maxSQLTranslatorRC | Default maximum return code for the sql translator step. | true
assembler_maxCICSTranslatorRC | Default maximum return code for the cics translator step. | true
assembler_maxRC | Default maximum return code for the Assembler step. | true
assembler_maxIDILANGX_RC | Default maximum return code for the debug IDILANGX sidefile generation step. | true
assembler_linkEditMaxRC | Default maximum return code for the linkEdit step. | true
assembler_impactPropertyList | List of build properties causing programs to rebuild when changed | false
assembler_impactPropertyListCICS | List of CICS build properties causing programs to rebuild when changed | false
assembler_impactPropertyListSQL | List of SQL build properties causing programs to rebuild when changed | false
assembler_dependencySearch | Assembler dependencySearch configuration to configure the SearchPathDependencyResolver. Format is a concatenated string of searchPath configurations. Strings representing the SearchPaths defined in `application-conf/application.properties`. | true
assembler_storeSSI | Flag to store abbrev git hash in ssi field in link step | true
assembler_deployType | default deployType for build output | true
assembler_deployTypeCICS | deployType for build output for build files where isCICS=true | true
assembler_deployTypeDLI | deployType for build output for build files with isDLI=true | true
assembler_scanLoadModule | Flag indicating to scan the load module for link dependencies and store in the application's outputs collection. | true
assembler_assemblySyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during assembly step | true
assembler_linkEditSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during linkEdit step | true

### BMS.properties
Application properties used by zAppBuild/language/BMS.groovy

Property | Description | Overridable
--- | --- | ---
bms_fileBuildRank | Default BMS program build rank. Used to sort BMS build file sub-list. Leave empty. | true
bms_maxRC | Default BMS maximum RC allowed. | true
bms_copyGenParms | Default parameters for the copybook generation step. | true
bms_compileParms | Default parameters for the compilation step. | true
bms_linkEditParms | Default parameters for the link edit step. | true
bms_impactPropertyList | List of build properties causing programs to rebuild when changed | false
bms_storeSSI | Flag to store abbrev git hash in ssi field in link step | true
bms_deployType | deployType for build output | true
bms_copy_deployType | deployType for generated copybooks | true


### Cobol.properties
Application properties used by zAppBuild/language/Cobol.groovy

Property | Description | Overridable
--- | --- | ---
cobol_fileBuildRank | Default Cobol program build rank. Used to sort Cobol build file sub-list. Leave empty. | true
cobol_dependencySearch | Cobol dependencySearch configuration to configure the SearchPathDependencyResolver. Format is a concatenated string of searchPath configurations. Strings representing the SearchPaths defined in `application-conf/application.properties`. | true
cobol_compilerVersion | Default Cobol compiler version. | true
cobol_compileMaxRC | Default compile maximum RC allowed. | true
cobol_linkEditMaxRC | Default link edit maximum RC allowed. | true
cobol_compileParms | Default base compile parameters. | true
cobol_compileCICSParms | Default CICS compile parameters. Appended to base parameters if has value.| true
cobol_compileSQLParms | Default SQL compile parameters. Appended to base parameters if has value. | true
cobol_compileErrorPrefixParms | IDz user build parameters. Appended to base parameters if has value. | true
cobol_linkEditParms | Default link edit parameters. | true
cobol_compileDebugParms | Default Debug compile parameters. Appended to base parameters if running with debug flag set. | true
cobol_storeSSI | Flag to store abbrev git hash in ssi field in link step | true
cobol_impactPropertyList | List of build properties causing programs to rebuild when changed | false
cobol_impactPropertyListCICS | List of CICS build properties causing programs to rebuild when changed | false
cobol_impactPropertyListSQL | List of SQL build properties causing programs to rebuild when changed | false
cobol_linkEdit | Flag indicating to execute the link edit step to produce a load module for the source file.  If false then a object deck will be created instead for later linking. | true
cobol_linkEditStream | Optional linkEditStream defining additional link instructions via SYSIN dd | true
cobol_linkDebugExit | linkEditStream to append a debug exit via SYSIN dd | true
cobol_deployType | default deployType for build output | true
cobol_deployTypeCICS | deployType for build output for build files where isCICS=true | true
cobol_deployTypeDLI | deployType for build output for build files with isDLI=true | true
cobol_scanLoadModule | Flag indicating to scan the load module for link dependencies and store in the application's outputs collection. | true
cobol_compileSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during compile step | true
cobol_linkEditSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during linkEdit step | true

### LinkEdit.properties
Application properties used by zAppBuild/language/LinkEdit.groovy

Property | Description | Overridable
--- | --- | ---
linkedit_fileBuildRank | Default link card build rank. Used to sort link card build sub-list. Leave empty. | true
linkedit_maxRC | Default link edit maximum RC allowed. | true
linkedit_parms | Default link edit parameters. | true
linkedit_impactPropertyList | List of build properties causing programs to rebuild when changed | false
linkedit_storeSSI | Flag to store abbrev git hash in ssi field in link step | true
linkedit_deployType | default deployType for build output | true
linkedit_deployTypeCICS | deployType for build output for build files where isCICS=true set as file property | true
linkedit_deployTypeDLI | deployType for build output for build files with isDLI=true set as file property | true
linkedit_scanLoadModule | Flag indicating to scan the load module for link dependencies and store in the application's outputs collection. | true
linkEdit_linkEditSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during linkEdit step | true

### PLI.properties
Application properties used by zAppBuild/language/LinkEdit.groovy

Property | Description | Overridable
--- | --- | ---
pli_fileBuildRank | Default PLI program build rank. Used to sort PLI program sub-list. Leave empty. | true
pli_dependencySearch | PLI dependencySearch configuration to configure the SearchPathDependencyResolver. Format is a concatenated string of searchPath configurations. Strings representing the SearchPaths defined in `application-conf/application.properties`. | true
pli_compilerVersion | Default PLI compiler version. | true
pli_compileMaxRC | Default compile maximum RC allowed. | true
pli_linkEditMaxRC | Default link edit maximum RC allowed. | true
pli_compileParms | Default base compile parameters. | true
pli_compileCICSParms | Default CICS compile parameters. Appended to base parameters if has value.| true
pli_compileSQLParms | Default SQL compile parameters. Appended to base parameters if has value. | true
pli_compileDebugParms | Default Debug compile parameters. Appended to base parameters if running with debug flag set. | true
pli_compileIMSParms | Default IMS compile parameters. Appended to parms for file with `isIMS` flag turned on. | true
pli_compileErrorPrefixParms | IDz user build parameters. Appended to base parameters if has value. | true
pli_impactPropertyList | List of build properties causing programs to rebuild when changed | false
pli_impactPropertyListCICS | List of CICS build properties causing programs to rebuild when changed | false
pli_impactPropertyListSQL | List of SQL build properties causing programs to rebuild when changed | false
pli_linkEditParms | Default link edit parameters. | true
pli_linkEditStream | Optional linkEditStream defining additional link instructions via SYSIN dd | true
pli_linkDebugExit | linkEditStream to append a debug exit via SYSIN dd | true
pli_storeSSI | Flag to store abbrev git hash in ssi field in link step | true
pli_impactPropertyList | List of build properties causing programs to rebuild when changed | false
pli_impactPropertyListCICS | List of CICS build properties causing programs to rebuild when changed | false
pli_impactPropertyListSQL | List of SQL build properties causing programs to rebuild when changed | false
pli_linkEdit | Flag indicating to execute the link edit step to produce a load module for the source file.  If false then a object deck will be created instead for later linking. | true
pli_deployType | default deployType for build output | true
pli_deployTypeCICS | deployType for build output for build files where isCICS=true | true
pli_deployTypeDLI | deployType for build output for build files with isDLI=true | true
pli_scanLoadModule | Flag indicating to scan the load module for link dependencies and store in the application's outputs collection. | true
pli_compileSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during compile step | true
pli_linkEditSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during linkEdit step | true

Property | Description | Overridable
--- | --- |
cpp_fileBuildRank | default C/CPP program build rank - used to sort language build file list leave empty - overridden by file properties if sorting needed | true
cpp_dependencySearch |  C/CPP dependencySearch configuration searchPath defined in application.properties | true
cpp_compileMaxRC |  default C/CPP maximum RCs allowed | true
cpp_impactPropertyList |  lists of properties which should cause a rebuild after being changed | false
cpp_compileParms |  default C/CPP compiler parameters | true
cpp_compileDebugParms | Compile Options for IBM Debugger. Assuming to keep Dwarf Files inside the load. If you would like to separate debug info, additional allocations needed (See C/CPP + Debugger libraries) | true
cpp_linkEditParms |  default LinkEdit parameters | true
cpp_linkEditStream | Optional linkEditStream defining additional link instructions via SYSIN dd, sample: cpp_linkEditStream=    INCLUDE SYSLIB(COBJT) | true
cpp_linkEdit |  execute link edit step | true
cpp_storeSSI |  store abbrev git hash in ssi field available for buildTypes impactBuild, mergeBuild and fullBuild | true
cpp_identifyLoad |  flag to generate IDENTIFY statement during link edit phase to create an user data record (IDRU) to "sign" the load module with an identify String: <application>/<abbreviatedGitHash> to increase traceability. default: true | true
cpp_deployType |  default deployType | true
cpp_deployTypeCICS |  deployType for build files with isCICS=true | true
cpp_deployTypeDLI |  deployType for build files with isDLI=true | true
cpp_scanLoadModule |  scan link edit load module for link dependencies | true
cpp_compileSyslibConcatenation |  additional libraries for compile SYSLIB concatenation, comma-separated | true
cpp_assemblySyslibConcatenation |  additional libraries for compile ASMLIB concatenation, comma-separated ASMLIB concatenation for C programs using the ASM option | true
cpp_linkEditSyslibConcatenation |  additional libraries for linkEdit SYSLIB concatenation, comma-separated | true

### bind.properties
Application properties used by zAppBuild/utilities/BindUtilities.groovy

Property | Description | Overridable
--- | --- | ---
bind_performBindPackage | Flag to perform DB2 bind package as part of a DBB User Build (default value:false) | true
bind_performBindPlan | Flag to perform DB2 bind plan as part of a DBB User Build (default value:false) | true
bind_jobCard | The Jobcard used for Bind jobs
bind_db2Location | The name of the DB2 subsystem | true
bind_collectionID | The Db2 collection (package) name | true
bind_plan | The Db2 plan name | true
bind_plan_pklist | The package list parameter for the Db2 plan | true
bind_packageOwner | The owner of the package, if left empty the user executing the command will be used | true
bind_qualifier | The value of the implicit qualifier | true
bind_maxRC | Default bind maximum RC allowed. | true

### MFS.properties
Application properties used by zAppBuild/language/MFS.groovy

Property | Description | Overridable
--- | --- | ---
mfs_fileBuildRank | Default MFS program build rank. Used to sort MFS build file sub-list. Leave empty. | true
mfs_phase1MaxRC | Default MFS Phase 1 maximum RC allowed. | true
mfs_phase2MaxRC | Default MFS Phase 2 maximum RC allowed. | true
mfs_phase2Execution | Flag if MFS Phase 2 process should be executed. Default: false | true
mfs_phase1Parms | Default parameters for the phase 1 step. | true
mfs_phase2Parms | Default parameters for the phase 2 step. | true
mfs_impactPropertyList | List of build properties causing programs to rebuild when changed | false
mfs_deployType | default deployType for build output | true

### DBDgen.properties
Application properties used by zAppBuild/language/DBDgen.groovy

Property | Description | Overridable
--- | --- | ---
dbdgen_fileBuildRank | Default build program build rank. Used to sort DBDgen build file sub-list. Leave empty. | true
dbdgen_pgmParms | Default DBDgen parameters. | true
dbdgen_linkEditParms | Default parameters for the link edit step. | true
dbdgen_compileErrorPrefixParms | Default parameters to support remote error feedback in user build scenarios | true
dbdgen_assemblerMaxRC | Default link edit maximum RC allowed. | true
dbdgen_linkEditMaxRC | Default link edit maximum RC allowed. | true
dbdgen_impactPropertyList | List of build properties causing programs to rebuild when changed | false
dbdgen_deployType | default deployType for build output | true


### PSBgen.properties
Application properties used by zAppBuild/language/PSBgen.groovy

Property | Description | Overridable
--- | --- | ---
psbgen_fileBuildRank | Default build program build rank. Used to sort DBDgen build file sub-list. Leave empty. | true
psbgen_pgmParms | Default PSBgen parameters. | true
psbgen_linkEditParms | Default parameters for the link edit step. | true
psbgen_compileErrorPrefixParms | Default parameters to support remote error feedback in user build scenarios | true
psbgen_runACBgen | Parameter if ACBgen should be executed right after PSBgen (default: true) | true
psbgen_assemblerMaxRC | Default link edit maximum RC allowed. | true
psbgen_linkEditMaxRC | Default link edit maximum RC allowed. | true
psbgen_impactPropertyList | List of build properties causing programs to rebuild when changed | false
psbgen_deployType | default deployType for build output | true

### ACBgen.properties
Application properties used by zAppBuild/language/PSBgen.groovy

Property | Description | Overridable
--- | --- | ---
acbgen_pgmParms | Default ACBgen parameters. | true
acbgen_pgmMaxRC | Default ACBgen maximum RC allowed. | true
acbgen_deployType | default deployType for build output | true

### TazUnitTest.properties
Application properties used by zAppBuild/language/TazUnitTest.groovy

Property | Description | Overridable
--- | --- | ---
tazunittest_maxPassRC | Default zUnit maximum RC allowed for a Pass. | true
tazunittest_maxWarnRC | Default zUnit maximum RC allowed for a Warninig (everything beyond this value will Fail). | true
tazunittest_jobCard  | Jobcard for TAZ Unit Test execution jobs | true
tazunittest_playbackFileExtension | Default zUnit Playback File Extension. | true
tazunittest_dependencySearch | Default zUnit dependencySearch configuration to configure the SearchPathDependencyResolver. Format is a concatenated string of searchPath configurations. Strings representing the SearchPaths defined in `application-conf/application.properties`.  | true
tazunittest_eqaplayParms | Default options passed to the TAZ runner procedure EQAPPLAY | true
tazunittest_userDebugSessionTestParm | Debug Tool Test parameter to initiate the debug session | true
tazunittest_CodeCoverageHost | Headless Code Coverage Collector host (if not specified IDz will be used for reporting) | true
tazunittest_CodeCoveragePort | Headless Code Coverage Collector port (if not specified IDz will be used for reporting) | true
tazunittest_CodeCoverageOptions | Headless Code Coverage Collector Options | true

### CRB.properties
Application properties used by zAppBuild/language/CRB.groovy

Property | Description | Overridable
--- | --- | ---
crb_maxRC | CICS Resource Builder maximum acceptable return code (default is 4 if not specified) | true

### REXX.properties
Application properties used by zAppBuild/language/REXX.groovy

Property | Description | Overridable
--- | --- | ---
rexx_compileMaxRC | Default compile maximum RC allowed. | true
rexx_linkEditMaxRC | Default link edit maximum RC allowed. | true
rexx_dependencySearch | Default REXX dependencySearch configuration to configure the SearchPathDependencyResolver. Format is a concatenated string of searchPath configurations. Strings representing the SearchPaths defined in `application-conf/application.properties`.  | true
rexx_compileParms | Default base compile parameters. | true
rexx_compiler | Default REXX compiler | true
rexx_linkEdit | Flag indicating to execute the link edit step to produce a compiled rexx for the source file. | true
rexx_linkEditParms | Default link edit parameters. | true
rexx_deployType | default deployType | true
rexx_cexec_deployType | default deployType CEXEC | true
rexx_compileSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during compile step | true
rexx_linkEditSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during linkEdit step | true

### Eastrieve.properties
Application properties used by zAppBuild/language/Easytrieve.groovy

Property | Description | Overridable
--- | --- | ---
easytrieve_compileMaxRC | Default compile maximum RC allowed. | true
easytrieve_linkEditMaxRC | Default link edit maximum RC allowed. | true
easytrieve_dependencySearch | Default dependencySearch configuration to configure the SearchPathDependencyResolver. Format is a concatenated string of searchPath configurations. Strings representing the SearchPaths defined in `application-conf/application.properties`.  | true
easytrieve_compileParms | Default base compile parameters. | true
easytrieve_linkEdit | Flag indicating to execute the link edit step to produce a compiled easytrieve for the source file. | true
easytrieve_linkEditParms | Default link edit parameters. | true
easytrieve_deployType | default deployType | true
easytrieve_compileSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during compile step | true
easytrieve_linkEditSyslibConcatenation | A comma-separated list of libraries to be concatenated in syslib during linkEdit step | true

### Transfer.properties
Application properties used by zAppBuild/language/Transfer.groovy

Property | Description | Overridable
--- | --- | ---
transfer_deployType | deployType | true
transfer_copyMode | Copy mode used during the copy to the target data set | true

### languageConfigurationMapping.properties
Sample language configuration mapping properties used by dbb-zappbuild/utilities/BuildUtilities.groovy.

This contains the mapping of the files and their corresponding Language Configuration properties files residing in `zAppBuild/build-conf/language-conf` to override the default file properties.

Example: The entry - `epsnbrvl.cbl=languageConfigProps01`, means the file properties of file `epsnbrvl.cbl` will be overridden by the properties mentioned in `zAppBuild/build-conf/language-conf/languageConfigProps01.properties`

See the [language configuration mapping documentation](../../docs/FilePropertyManagement.md#language-configuration-mapping) for more details on how to enable and use language configuration mapping.
