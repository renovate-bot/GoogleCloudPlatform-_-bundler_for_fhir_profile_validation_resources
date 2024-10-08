# Bundler for FHIR profile validation resources

## Summary

This is a utility that helps create a FHIR `transaction` [Bundle](https://build.fhir.org/bundle.html) of a set of profile validation resources, such that execution of the bundle can load the resources atomically into a FHIR store.

## Getting Started

Being a Python-based utility, you will have to have [Python](https://www.python.org/downloads/) installed.

Otherwise, the utility uses libraries that are part of the Python [standard library](https://docs.python.org/3/library/index.html) - `argparse`, `hashlib`, `json`, `os` and `uuid`.

### Details

You'll need to run the utility with these input arguments:

- `--input_dir`: the directory containing all of the loose FHIR profile validation resource JSONs.
- `--output_dir`: the directory into which to output the bundle of profile validation resources.
- `--generate_global_array`: optional boolean flag to indicate whether or not you'd like the utility to populate the `global` array in the `ImplementationGuide` resource containing references to all `StructureDefinition` resources in the inputs, which is [needed by the Google Cloud Healthcare API](https://cloud.google.com/healthcare-api/docs/how-tos/fhir-profiles#configure_your_implementation_guide). The default value is True. If you've already done this yourself, you can pass `--no-generate_global_array`.

### Feedback/contributions

We welcome any help with improving the tool! Please see the CONTRIBUTING file for guidelines.

## License

Apache License, Version 2.0

## Usage notes

- The implementation guide feature in Google Cloud Healthcare FHIR stores are
  used for *profile validation*. If you try to create a bundle from a guide
  package that contains a purely descriptive `ImplementationGuide` (i.e. one
  that solely provides textual information without any associated validation
  resources like `StructureDefinition`s, `ValueSet`s, etc.), it will not have
  much effect after upload into a FHIR store.
- `ImplemenationGuide`s may contain `StructureDefinition`s that should not be
  applied to all resources, either because they do not describe a resource
  (`StructureDefinition`s for FHIR data types), or they only apply in specific
  scenarios. To distinguish between these different use cases, the FHIR store
  uses the `ImplementationGuide.global` field to identify resource profiles that
  all incoming resources should be validated against.

  The bundler will help you assemble resource-based `StructureDefinition`s into
  the `ImplementationGuide.global` field, should you instruct it to. You will
  have to ensure other `StructureDefinition`s are referenced appropriately
  according to your use case.
