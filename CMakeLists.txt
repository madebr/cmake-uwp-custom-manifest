cmake_minimum_required(VERSION 3.26)
project(uwp_test CXX)

file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/main.cpp" [[
  #include <wrl.h>
  #ifdef WINAPI_FAMILY_PARTITION 
  int main(Platform::Array<Platform::String^>^ args) {
    return 0;
  }
  #else
  int main(int argc, char *argv[]) {
    return 0;
  }
  #endif
]])

file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/Package.appxmanifest.in" [[
<?xml version="1.0" encoding="utf-8"?>
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  IgnorableNamespaces="mp uap">
  <Identity Name="17d1193c-d60c-4a10-9469-58fda932e9ed" Publisher="O=org.example" Version="1.0.0.0" />
  <mp:PhoneIdentity PhoneProductId="17d1193c-d60c-4a10-9469-58fda932e9ed" PhonePublisherId="00000000-0000-0000-0000-000000000000"/>
  <Properties>
    <DisplayName>@TARGET@</DisplayName>
    <PublisherDisplayName>example.org</PublisherDisplayName>
    <Logo>logo.png</Logo>
  </Properties>
  <Dependencies>
    <TargetDeviceFamily Name="Windows.Universal" MinVersion="10.0.0.0" MaxVersionTested="10.0.0.0" />
  </Dependencies>
  <Resources>
    <Resource Language="x-generate" />
  </Resources>
  <Applications>
    <Application Id="App" Executable="@TARGET@.exe" EntryPoint="@TARGET@.App">
      <uap:VisualElements DisplayName="@TARGET@" Description="@TARGET@" BackgroundColor="#FFFFFF" Square150x150Logo="square-150x150.png" Square44x44Logo="square-44x44.png">
        <uap:SplashScreen Image="splash.png" />
      </uap:VisualElements>
    </Application>
  </Applications>
</Package>
]])


set(number_targets "2" CACHE STRING "Number of targets to build")
set(i 0)
while(i LESS "${number_targets}")
  set(TARGET "target${i}")
  #set(target_dir "${CMAKE_CURRENT_BINARY_DIR}/${TARGET}.dir")
  configure_file("${CMAKE_CURRENT_BINARY_DIR}/Package.appxmanifest.in" "${CMAKE_CURRENT_BINARY_DIR}/${TARGET}.appxmanifest" @ONLY)
  add_executable("${TARGET}"
    "${CMAKE_CURRENT_BINARY_DIR}/main.cpp"
    "${CMAKE_CURRENT_BINARY_DIR}/${TARGET}.appxmanifest"
    "logo.png"
    "splash.png"
    "square-44x44.png"
    "square-150x150.png"
  )
  target_compile_options("${TARGET}" PRIVATE "/ZW")
  math(EXPR i "${i} + 1")
endwhile()