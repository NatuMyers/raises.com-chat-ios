require_relative '../node_modules/react-native/scripts/react_native_pods'
require_relative '../node_modules/@react-native-community/cli-platform-ios/native_modules'
require File.join(File.dirname(`node --print "require.resolve('expo/package.json')"`), "scripts/autolinking")

platform :ios, '12.0'
install! 'cocoapods', :deterministic_uuids => false

def all_pods
  pod 'React-jsi', :path => '../node_modules/react-native/ReactCommon/jsi', :modular_headers => true
  pod 'simdjson', path: '../node_modules/@nozbe/simdjson'
  pod 'ReactNativeUiLib', :path => '../node_modules/react-native-ui-lib/lib'
  $RNFirebaseAnalyticsWithoutAdIdSupport = true
  config = use_native_modules!

  use_expo_modules!
  post_integrate do |installer|
    begin
      expo_patch_react_imports!(installer)
    rescue => e
      Pod::UI.warn e
    end
  end

  flags = get_default_flags()
  
  use_react_native!(
    :path => config[:reactNativePath],
    :hermes_enabled => true, # flags[:hermes_enabled],
    :fabric_enabled => flags[:fabric_enabled],
    :app_path => "#{Pod::Config.instance.installation_root}/.."
  )

  # Enable and run pods again, if you want to use Flipper
  # use_flipper!()
end

abstract_target 'defaults' do
  # force use our own JitsiMeetSDK
  pod 'JitsiMeetSDK', :git => 'https://github.com/RocketChat/jitsi-meet-ios-sdk-releases.git'

  all_pods

  target 'RocketChatRN' # Experimental app
  target 'Rocket.Chat' # Official app
  target 'ShareRocketChatRN'
  target 'NotificationService'
end

post_install do |installer|
  react_native_post_install(installer)

  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['APPLICATION_EXTENSION_API_ONLY'] = 'NO'
      case target.name
      when 'RCT-Folly'
        config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '9.0'
      else
        config.build_settings.delete('IPHONEOS_DEPLOYMENT_TARGET')
      end
    end
  end
end
