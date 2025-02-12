node {
    // 定义环境变量
    def WORKSPACE = env.WORKSPACE
    def XCODE_PROJECT = "Pipeline-projects.xcodeproj" // 如果你的项目使用 .xcodeproj
    def XCODE_SCHEME = "Pipeline-projects"
    def XCODE_CONFIGURATION = "Release"
    def CODE_SIGN_IDENTITY = "Apple Development: 1143685275@qq.com (CAXWWGW2TG)" // 替换为你的 Apple Developer Team ID
    def PROVISIONING_PROFILE = "457070a9-b187-4d3f-b274-d9b0816ca645" // 替换为你的 Provisioning Profile 名称
    def EXPORT_PLIST_PATH = "exportOptions.plist"
    def FASTLANE_LANE = "appstore" // Fastlane 的 lane 名称

    try {
        stage('Checkout') {
            echo "Checking out code from Git repository..."
	    checkout scm
        }

	stage('Install Certificates') {
            // 安装 .p12 证书
            withCredentials([file(credentialsId: 'P12_FILE', variable: 'P12_FILE')]) {
                    sh """
                    security import ${P12_FILE} -k ~/Library/Keychains/login.keychain -T /usr/bin/codesign
                    """
            }
    	}

	stage('Clean') {
            // 清理项目
            sh """
            xcodebuild clean -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -destination 'platform=iOS Simulator,name=iPhone 14,OS=16.4'
            """
        }

        stage('Build') {
            echo "Building the project..."
            sh """
            xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -configuration ${XCODE_CONFIGURATION} -sdk iphonesimulator build
	    """
        }

        stage('Test') {
            echo "Running tests..."
            sh """
            xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -sdk iphonesimulator -destination 'platform=iOS Simulator,name=iPhone 14,OS=16.4' test
            """
        }

        stage('Archive & Export IPA') {
            echo "Archiving the project and exporting IPA..."
            sh """
            xcodebuild archive -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -configuration ${XCODE_CONFIGURATION} -archivePath ${WORKSPACE}/build/Pipeline-projects.xcarchive"
            """
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
