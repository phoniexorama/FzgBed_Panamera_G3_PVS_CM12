@Library('cm_shared_library') _
pipeline {
    agent {
        label 'CarMakerServer' // Label for Windows agent
    }
    environment {
        TEMPLATE_FOLDER_PATH = "${WORKSPACE}\\Data\\TestRun\\Lenkwinkelrampe_Template"
        TEMPLATE_FILE = "${WORKSPACE}\\Template.ts"
        DESTINATION_FOLDER = "${WORKSPACE}\\Data\\TestRun"
        VEHICLE_FOLDER_PATH = "${WORKSPACE}\\Data\\Vehicle"
        OUTPUT_FOLDER = "${WORKSPACE}\\SimOutput\\ENGPMAKNB022"
        LOG_FOLDER = "${WORKSPACE}\\SimOutput\\ENGPMAKNB022\\Log"
        VFF_FOLDER_PATH = "${WORKSPACE}\\Data\\Vehicle"
        DAT_FOLDER_PATH = "${WORKSPACE}\\SimOutput\\ENGPMAKNB022\\log"
        EXCEL_FOLDER_PATH = "${WORKSPACE}\\VehicleInfoExcel"
        BATCH_SCRIPT_PATH = "${WORKSPACE}\\${BATCH_SCRIPT_NAME}"
        TEST_SERIES_FOLDER_PATH = "${WORKSPACE}\\Data\\TestRun"
        FORMAT_FILE_CONFIG_PATH = "${WORKSPACE}\\Data\\Config\\Lenkwinkelrampe_Temp"
        BATCH_SCRIPT_NAME = "carmaker_${PROJECT_NAME}.bat"
        TCL_SCRIPT_NAME = 'CMGUI_RemCtrl.tcl'
        MODELCHECK_PATH = "${WORKSPACE}\\ModelCheck"
        PROJECT_NAME = 'Frg-Bedatung_Cayenne_E4_CM12'
    }

    stages {
        
        stage('Dat File Generator') {
            steps {
                script {
                    // Call the Python script for dat file generation
                    //bat "python carmakerdatfilegenerator.py"
                    stepCarMakerDatFileGenerator()
                }
            }
        }

        stage('Excelfile Generator') {
            steps {
                script {
                    // Call the Python script for excel file generation
                    //bat "python autoexcelfilegenerator.py"
                    stepAutoExcelFileGenerator()
                }
            }
        }

        stage('Testseries Generator') {
            steps {
                script {
                    // Call the Python script for test series generation
                    //bat "python testseriesgenerator.py"
                    def fileName = 'Template.ts'
                    def sourcePath = "template//${env.PROJECT_NAME}//Template.ts"
                    def targetPath = "${env.WORKSPACE}" // Use Jenkins workspace as target
                    
                    // Call the copyFile function to copy the file
                    stepCopyFile(fileName, sourcePath, targetPath)

                    stepTestSeriesGenerator()
                }
            }
        }
        
        stage('Run TestManager') {
            steps {
                script {
                    // Call the Python script for running test manager
                    //bat "python runtestmanager.py"
                    def fileName = "${env.BATCH_SCRIPT_NAME}"
                    def sourcePath = "bat//${env.BATCH_SCRIPT_NAME}"
                    def targetPath = "${env.WORKSPACE}" // Use Jenkins workspace as target
                    def tclFile = "${env.TCL_SCRIPT_NAME}"
                    def tclSourcePath = "tcl//${env.TCL_SCRIPT_NAME}"
                    def tclDesPath = "${env.WORKSPACE}\\Data\\Script\\Examples"
                    
                    // Call the copyFile function to copy the file
                    stepCopyFile(fileName, sourcePath, targetPath)
                    
                    // Call the copyFile function to copy the tcl file
                    stepCopyFile(tclFile, tclSourcePath, tclDesPath)
                    
                    stepRunTestManager()
                }
            }
        }

        stage('Run ModelCheck') {
            steps {
                script {
                    // Call the Python script for running test manager
                    //bat "python modelcheck.py                    
                    stepRunModelCheck()
                }
            }
        }

        stage('Pack Artifacts') {
            steps {
                script {

                   // Get the WORKSPACE environment variable
                    def workspace = env.WORKSPACE
                    
                    // Define the last name from the workspace path
                    def lastIndex = workspace.lastIndexOf('\\')
                    def lastName = workspace.substring(lastIndex + 1)
                    println "Last Name: ${lastName}"
                    
                    // Define main directory path based on last name
                    def mainDirectoryPath = "${workspace}\\${lastName}"
                    
                    // Check and create main directory if not exists
                    bat "mkdir \"${mainDirectoryPath}\""
                    
                    // Define the Data directory path
                    def dataDirectoryPath = "${mainDirectoryPath}\\Data"
                    
                    // Check and create Data directory if not exists
                    bat "mkdir \"${dataDirectoryPath}\""
                    
                    // Define source paths and corresponding destination paths
                    def sourceDestinationMappings = [
                        "${workspace}\\Data\\TestRun": "${dataDirectoryPath}\\TestRun",
                        "${workspace}\\Data\\Chassis": "${dataDirectoryPath}\\Chassis",
                        "${workspace}\\Data\\Tire": "${dataDirectoryPath}\\Tire",
                        "${workspace}\\Data\\Pic": "${dataDirectoryPath}\\Pic",
                        "${workspace}\\Data\\Vehicle": "${dataDirectoryPath}\\Vehicle",
                        "${workspace}\\Movie": "${mainDirectoryPath}\\Movie",
                        "${workspace}\\doc": "${mainDirectoryPath}\\doc",
                        "${workspace}\\ModelCheck": "${mainDirectoryPath}\\doc\\ModelCheck"
                    ]
                                    
                    // Copy folders with recursive content from source paths to corresponding destination paths
                    stepCopyFolder(sourceDestinationMappings)
                }
            }
        }    
    }
}
