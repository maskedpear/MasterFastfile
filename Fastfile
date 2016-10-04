fastlane_version "1.55.0"

default_platform :ios

  before_all do
  end

  lane :test do
    setup()
    scan
  end

  lane :hockey do
    setup()
    scan
    deploy_hockey()
  end

  lane :hockey_no_test do
    setup()
    deploy_hockey()
  end

  def deploy_hockey()
    icon_overlay(version: get_version_number)
    set_build_number()
    update_info_plist
    build_with_gym()
    upload_to_hockey()
  end

  def build_with_gym()
    provisioning_profile_name = ENV['TAB_PROVISIONING_PROFILE']
    if provisioning_profile_name != nil
      xcconfig_filename = "./fastlane/TAB.release.xcconfig"
      File.write(xcconfig_filename, "PROVISIONING_PROFILE_SPECIFIER = #{provisioning_profile_name}\n")
      gym(use_legacy_build_api: true, xcconfig: xcconfig_filename)
    else
      gym(use_legacy_build_api: true)
    end
  end

  def upload_to_hockey()
    custom_notes = ENV['TAB_HOCKEY_RELEASE_NOTES'] || ""
    notes = custom_notes == "" ? create_change_log() : custom_notes
    hockey(notes_type: "0", notes: notes)
  end

  def setup()
    ENV['SCAN_SCHEME'] = ENV['GYM_SCHEME']
    if ENV['SCAN_DEVICE'] == nil
      ENV['SCAN_DEVICE'] = "iPhone 6 (9.3)"
    end
    if is_ci && ENV['TAB_XCODE_PATH'] != nil
      xcode_select(ENV['TAB_XCODE_PATH'])
    end
  end

  def set_build_number()
    build_number = "0"
    use_timestamp = ENV['TAB_USE_TIME_FOR_BUILD_NUMBER'] || false
    if use_timestamp
      build_number = Time.now.strftime("%y%m%d%H%M")
    else
      build_number = ENV['BUILD_NUMBER']
    end
    increment_build_number(build_number: build_number)
  end

  def create_change_log()
    cmd = "git log --after={1.day.ago} --pretty=format:'%an%x09%h%x09%cd%x09%s' --date=relative"
    output = `#{cmd}`
    return (output.length == 0) ? "No Changes" : output
  end

  after_all do |lane|
  end

  error do |lane, exception|
  end
