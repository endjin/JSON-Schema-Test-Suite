These tests are intended to validate that implementations are correctly generating output in accordance with the specification.

Output was initially specified with draft 2019-09.  It remained largely unchanged for draft 2020-12, but will receive an update with the next release.

***NOTE** Although the formats didn't change between 2019-09 and 2020-12, the tests are replicated for 2020-12 because the `$schema` is different and implementations may (but shouldn't) produce different output.*

## Organization

The tests are organized by specification release and then into two categories: content and structure.

Content tests verify that the keywords are producing the correct annotations and/or error messages.  Since there are no requirements on the content of error messages, there's not much that can be verified for them, but it is possible to identify when a error message _could_ be present.  Primarily, these tests need to extensively cover the annotation behaviors of each keyword.  The only output format needed for these tests is `basic` for 2019-09/2020/12 and `list` for later versions.

Structure tests verify that the structures of the various formats (i.e. `flag`, `basic`, `detailed`, `verbose` for 2019/2020 and `flag`, `list`, `hierarchical` for later versions) are correct.  These tests don't need to cover each keyword; rather they need to sufficiently cover the various aspects of building the output structures by using whatever keywords are necessary to do so.

In each release folder, you'll also find an _output-schema.json_ file that contains the schema from the specification repo that describes output for that release.  This schema will need to be loaded as the tests reference it.

## Test Files

The content of a test file is the same as the validation tests in `tests/`, however an `output` property has been added to each test case.

The `output` property itself has a property for each of the output formats where the value is a schema that will successfully validate for compliant output.  For the content tests, only `basic`/`list` needs to be present.

## Other notes

### Ambiguity around 2020-09/2020-12 `basic`

The 2019-09/2020-12 specs don't define the structure of `basic` very thoroughly.  Specifically there is a nuance where if the list contains a single output node, there are two possible structures, given the text:

- the output node for the root schema appears in the list with a containing node that just has a `valid` property
    ```json
    {
        "valid": false,
        "errors": [
            {
                "valid": false,
                "keywordLocation": "",
                "absoluteKeywordLocation": "https://json-schema.org/tests/content/draft2019-09/general/0",
                "instanceLocation": ""
            }
        ]
    }
    ```
- the entire structure is collapsed to just the root output node as `detailed` would do.
    ```json
    {
        "valid": false,
        "keywordLocation": "",
        "absoluteKeywordLocation": "https://json-schema.org/tests/content/draft2019-09/general/0",
        "instanceLocation": ""
    }
    ```
As the Test Suite should not prefer one interpretation over another, these cases need to be tested another way.

A simple solution (though there are likely others) is to force a second output unit by adding an `"anyOf": [ true ]`.  This has no impact on the validation result while adding superfluous structure to the output that avoids the above ambiguous scenario.  The test schema should still be targeted on what's being tested and ignore any output units generated by this extra keyword.

## Contributing

Of course, first and foremost, follow the [Contributing guide](/CONTRIBUTING.md).

When writing test cases, try to keep output validation schemas targeted to verify a single requirement.  Where possible (and where it makes sense), create multiple tests to cover multiple requirements.  This will help keep the output validation schemas small and increase readability.  (It also increases your test count. 😉)

For the content tests, there is also a _general.json_ file that contains tests that do not necessarily pertain to any single keyword.
<!-- This general.json file may be added to the structure tests later, but I haven't gotten to them yet, so I don't know. -->
