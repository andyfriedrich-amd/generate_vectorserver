<h1>Generating docs for TensorFlowLiteSwift</h1>
<p>Documentation is generated via <a href="https://github.com/realm/jazzy">Jazzy</a> (Googlers
see cr/363774564 for more details).</p>
<p>To browse the Swift reference documentation visit
https://www.tensorflow.org/lite/api_docs/swift.</p>
<p>This directory contains a dummy Xcode project for generating documentation for
TensorFlowLiteSwift via Jazzy, an open-source tool that hooks into Xcode's
build tooling to parse doc comments. Unfortunately, TensorFlowLiteSwift is not
primarily developed via xcodebuild, so the docs build can potentially become
decoupled from upstream TensorFlowLiteSwift development.</p>
<p>Known issues:
- Every new file added to TensorFlowLiteSwift's BUILD must also manually be
  added to this Xcode project.
- This project (and the resulting documentation) does not split types  by
  module, so there's no way to tell from looking at the generated documentation
  which modules must be included in order to access a specific type.
- The TensorFlowLiteC dependency is included in binary form, contributing
  significant bloat to the git repository since each binary contains unused
  architecture slices.</p>
<p>To generate documentation outside of Google, run jazzy as you would on any other
Swift module:</p>
<p><code>jazzy \
  --swift-build-tool xcodebuild \
  --module "TensorFlowLiteSwift" \
  --author "The TensorFlow Authors" \
  --sdk iphoneos \</code></p>