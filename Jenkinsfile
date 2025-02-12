node {
    // 定义环境变量
    def WORKSPACE = env.WORKSPACE
    def XCODE_PROJECT = "Pipeline-projects.xcodeproj" // 如果你的项目使用 .xcodeproj
    def XCODE_SCHEME = "Pipeline-projects"
    def XCODE_CONFIGURATION = "Release"
    def EXPORT_PLIST_PATH = "exportOptions.plist"
    def FASTLANE_LANE = "appstore" // Fastlane 的 lane 名称

    try {
        stage('Checkout') {
            echo "Checking out code from Git repository..."
            git branch: 'master', url: 'https://github.com/ShiWen1520/Pipeline-projects.git'
        }

        stage('Build') {
            echo "Building the project..."
            sh "xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -configuration ${XCODE_CONFIGURATION} -sdk iphonesimulator build"
        }

        stage('Test') {
            echo "Running tests..."
            sh "xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -sdk iphonesimulator -destination 'platform=iOS Simulator,name=iPhone 14,OS=16.4' test"
        }

        stage('Archive & Export IPA') {
            echo "Archiving the project and exporting IPA..."
            sh "xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -configuration ${XCODE_CONFIGURATION} -archivePath ${WORKSPACE}/build/Pipeline-projects.xcarchive archive"
            sh "xcodebuild -exportArchive -archivePath ${WORKSPACE}/build/Pipeline-projects.xcarchive -exportPath ${WORKSPACE}/build -exportOptionsPlist ${EXPORT_PLIST_PATH}"
        }

        stage('Deploy to App Store Connect') {
            echo "Deploying to App Store Connect using Fastlane..."
            // sh "fastlane ${FASTLANE_LANE}"
        }

        echo "Pipeline succeeded!"
    } catch (Exception e) {
        echo "Pipeline failed: ${e}"
        currentBuild.result = 'FAILURE'
    } finally {
        echo "Cleaning up workspace..."
        cleanWs()
    }
}
