# OpenDisplay Monterey Intel - first-pass source backport

Status: UNCOMPILED / UNTESTED. This is not an installable application and is
not a claim that extended-display mode works on a 2015 Mac.

Replace the corresponding files in a COPY of your repository with the files
in this archive. Keep their folder names. Alternatively, apply monterey.patch
at the repository root using git apply --check followed by git apply.
Do not apply both methods.

Changes:
- Sender and test deployment targets: macOS 12.3.
- Sender build architecture: x86_64 only (iOS target unchanged).
- Monterey uses the existing control window in Dock mode, not MenuBarExtra.
- Replace newer UI components and duration-based sleep calls in these two files.
- Gate newer capture-error and frame-metadata handling behind macOS 15.
  This deliberately skips wrong-display frame-metadata detection on older macOS.
- Disable starting the updater automatically for this experimental backport.
  Do not use manual Check for Updates: its upstream feed is not a Monterey feed.
- Preserve the existing Intel H.264 selection, extended-display protocol,
  networking, virtual-display calls and stream-selection policy.

IMPORTANT remaining work:
Only the three uploaded files have been edited. Other Mac/Shared Swift files
may still use newer APIs, including Task.sleep(for:), and need similar changes.
The private CGVirtualDisplay API must be tested on Monterey on the actual Mac.
No macOS SDK, Xcode compiler, signing or hardware test was available here.
No fixed 30fps/resolution limit has been implemented.
This retains Swift 5.9: the build host needs a suitable Xcode toolchain, which
may require a newer build Mac than the Monterey target Mac.

Build/check on a compatible Xcode build host with XcodeGen installed:
    xcodegen generate
    xcodebuild -project OpenSidecar.xcodeproj -scheme OpenSidecarMac \
      -configuration Debug -destination 'generic/platform=macOS' \
      ARCHS=x86_64 CODE_SIGNING_ALLOWED=NO build

This command is a compile check, not a signed/notarized installer workflow.
Send the first compiler errors for the next compatibility pass. Do not lower
availability declarations in other files without auditing the underlying APIs.
