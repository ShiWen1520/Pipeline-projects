node {
    // 定义环境变量
    def WORKSPACE = env.WORKSPACE
    def XCODE_PROJECT = "Pipeline-projects.xcodeproj" // 如果你的项目使用 .xcodeproj
    def XCODE_SCHEME = "Pipeline-projects"
    def XCODE_CONFIGURATION = "Release"
    def teamID = "世闻 郑 (8N83MF6J6M)" // 替换为你的 Apple Developer Team ID
    def provisioningProfile = "com.swyx.Pipeline-projects" // 替换为你的 Provisioning Profile 名称
    def EXPORT_PLIST_PATH = "exportOptions.plist"
    def FASTLANE_LANE = "appstore" // Fastlane 的 lane 名称

    try {
        stage('Checkout') {
            echo "Checking out code from Git repository..."
	    checkout scm
        }

	stage('Install Certificates') {
            // 安装 .p12 证书
            sh '''
            security import com.swyx.Pipeline-projects.p12 -k ~/Library/Keychains/login.keychain -T /usr/bin/codesign
        	'''
            // 安装 Provisioning Profile
            sh '''
            mkdir -p ~/Library/MobileDevice/Provisioning Profiles
            cp YourProfile.mobileprovision ~/Library/MobileDevice/Provisioning Profiles/
            '''
    	}

	stage('Clean') {
            // 清理项目
            sh """
            xcodebuild clean -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -destination 'platform=iOS Simulator,name=iPhone 14,OS=16.4'
            """
        }

        stage('Build') {
            echo "Building the project..."
            sh "xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -configuration ${XCODE_CONFIGURATION} -sdk iphonesimulator DEVELOPMENT_TEAM=${teamID} PROVISIONING_PROFILE_SPECIFIER=${provisioningProfile} build"
        }

        stage('Test') {
            echo "Running tests..."
            sh "xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -sdk iphonesimulator -destination 'platform=iOS Simulator,name=iPhone 14,OS=16.4' DEVELOPMENT_TEAM=${teamID} PROVISIONING_PROFILE_SPECIFIER=${provisioningProfile} test"
        }

        stage('Archive & Export IPA') {
            echo "Archiving the project and exporting IPA..."
            sh "xcodebuild -project ${XCODE_PROJECT} -scheme ${XCODE_SCHEME} -configuration ${XCODE_CONFIGURATION} -archivePath ${WORKSPACE}/build/Pipeline-projects.xcarchive DEVELOPMENT_TEAM=${teamID} PROVISIONING_PROFILE_SPECIFIER=${provisioningProfile} archive"
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
