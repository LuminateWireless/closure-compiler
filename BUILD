# Bazel(http://bazel.io) BUILD file for closure-compiler
licenses(['notice'])

load('/tools/build_rules/proto_library', 'proto_library')

genrule(
  name = 'externs_zip',
  outs = [ 'externs.zip' ],
  srcs = glob(['externs/**/*.js']),
  cmd = "TOP=$$PWD; cd third_party/js/closure-compiler/externs; zip $$TOP/$@ *.js */*.js",
)

# Create a jar that contains 'externs.zip', which will be loaded by
# AbstractCommandLineRunner.java
java_library(
  name = 'externs',
  resources = [ ':externs_zip' ],
  resource_strip_prefix = PACKAGE_NAME,
)

java_library(
  name = 'rhino',
  srcs = glob([
    'src/com/google/javascript/rhino/**/*.java'
  ], exclude=[
    'src/com/google/javascript/rhino/testing/**/*.java',
  ]),
  deps = [
    '//third_party/java/jsr305_annotations',
    '//third_party/java/guava',
  ],
  resources = glob([
    'src/com/google/javascript/rhino/**/*.properties'
  ]),
  resource_strip_prefix = 'third_party/js/closure-compiler/src'
)

java_library(
  name = 'sourcemap',
  srcs = glob([
    'src/com/google/debugging/sourcemap/**/*.java'
  ], exclude=[
  ]),
  deps = [
    ':mapping_proto_java',
    '//third_party/java/jsr305_annotations',
    '//third_party/java/guava',
    '//third_party/java/gson',
  ],
)

java_library(
  name = 'jscomp',
  visibility = ['//visibility:public'],
  srcs = glob([
    'src/com/google/javascript/jscomp/**/*.java',
    'src/com/google/javascript/refactoring/*.java',
  ], exclude=[
    'src/com/google/javascript/jscomp/gwt/**/*.java',
    'src/com/google/javascript/jscomp/ant/**/*.java',
    'src/com/google/javascript/jscomp/debugger/**/*.java',
    'src/com/google/javascript/jscomp/testing/**/*.java',
  ]),
  resources = glob([
    'src/com/google/javascript/jscomp/**/*.properties',
    'src/com/google/javascript/jscomp/**/*.txt',
  ]),
  resource_strip_prefix = PACKAGE_NAME + '/src',
  deps = [
    '//third_party/java/args4j',
    '//third_party/java/gson',
    '//third_party/java/guava',
    '//third_party/java/jsr305_annotations',
    '//third_party/protobuf:java_proto',
    ':externs',
    ':conformance_proto_java',
    ':function_info_proto_java',
    ':instrumentation_template_proto_java',
    ':mapping_proto_java',
    ':rhino',
    ':sourcemap',
  ],
  exports = [
    ':rhino',
    ':mapping_proto_java',
  ],
)

proto_library(
  name = 'mapping_proto',
  src = 'src/com/google/debugging/sourcemap/proto/mapping.proto',
  generate_cc = False,
  generate_cc_alwayslink = False,
  generate_java = True,
)

proto_library(
  name = 'conformance_proto',
  src = 'src/com/google/javascript/jscomp/conformance.proto',
  generate_cc = False,
  generate_cc_alwayslink = False,
  generate_java = True,
)

proto_library(
  name = 'function_info_proto',
  src = 'src/com/google/javascript/jscomp/function_info.proto',
  generate_cc = False,
  generate_cc_alwayslink = False,
  generate_java = True,
)

proto_library(
  name = 'instrumentation_template_proto',
  src = 'src/com/google/javascript/jscomp/instrumentation_template.proto',
  generate_cc = False,
  generate_cc_alwayslink = False,
  generate_java = True,
)

java_binary(
  name = 'closure-compiler',
  main_class = 'com.google.javascript.jscomp.CommandLineRunner',
  visibility = ['//visibility:public'],
  runtime_deps = [':jscomp'],
)
