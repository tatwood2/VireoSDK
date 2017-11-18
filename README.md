# Vireo
[![NPM Version](https://img.shields.io/npm/v/vireo.svg)](https://www.npmjs.com/package/vireo)
[![Travis Build Status](https://travis-ci.org/ni/VireoSDK.svg)](https://travis-ci.org/ni/VireoSDK)
[![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/ni/VireoSDK?svg=true)](https://ci.appveyor.com/project/vireobot/vireosdk)
[![Dependencies Status](https://david-dm.org/ni/VireoSDK/status.svg)](https://david-dm.org/ni/VireoSDK)
[![Development Dependencies Status](https://david-dm.org/ni/VireoSDK/dev-status.svg)](https://david-dm.org/ni/VireoSDK?type=dev)
[![Repository Size](https://reposs.herokuapp.com/?path=ni/VireoSDK&style=flat&color=brightgreen)](https://github.com/ruddfawcett/reposs)
[![NPM Downloads](https://img.shields.io/npm/dt/vireo.svg)](https://www.npmjs.com/package/vireo)

A compact parallel execution runtime for VIs saved in VI assembly format (.via files).

The Vireo project provides a subset of LabVIEW runtime functionality for smaller targets. Example usages are for WebVIs and the EV3 support in the _LabVIEW Module for LEGO® MINDSTORMS®_. The LabVIEW features supported is primarily defined by the features needed for the VIA files generated by LabVIEW.

# Building

Requirements:
- [Node](https://nodejs.org/en/) (> v4.4.7)
- [Emscripten](https://github.com/kripken/emscripten) (v1.36.0)
- make

Setup:
- `npm install`

Setup (Windows):
You need some UNIX command tools. [Chocolatey](https://chocolatey.org/) is a good option to get them. Once you have it, run:
- `choco install make`
- `choco install gnuwin32-coreutils.portable`

#### Visual Studio
Works with Visual Studio 2013 and 2015 (see the `Vireo_VS` folder).

### Easy Build
*`esh & vireo.js`*

From the root directory:
```shell
$ make
```
The `esh` and `vireo.js` files will be put into the `./dist` directory based on the build configuration.

For example the default vireo.js output of `TARGET=asmjs-unknown-emscripten` and `BUILD=release` will be:
`./dist/asmjs-unknown-emscripten/release/vireo.js`

#### Build Native Binary
```shell
$ make native
```

#### Build JavaScript File
```shell
$ make js
```

To create a debug build of vireo.js you can change the BUILD variable passed to the make command. For example:
```shell
$ make js BUILD=debug
```

# Testing
From the root directory:

`$ make test` - Runs all tests for Native and JS

## Running Tests
From within the `test-it` directory:

#### Against esh target (`-n`)
```shell
$ ./test.js -n
```

#### Against vireo.js target (`-j`)
```shell
$ ./test.js -j
```

#### Running Test Suites (`-t [test suite]`)
```shell
$ ./test.js -n -t <test suite>
```

#### Run Individual Tests (`[Test].via`)
```shell
$ ./test.js HelloWorld.via
```

#### Listing Out Tests (`-l [test suite]`)
Since the test suites can be created recursively from other test suites in the configuration file, the `-l` command line argument will list out all of the tests to be run with the test suite name provided. Example:
```shell
$ ./test.js -l native
```
Will list out all of the tests that would be run against the `native` test suite.

## Test Help
```shell
$ ./test.js -h
Usage: ./test.js [options] [via test files]
Options:
 -n                  Run the tests against the native vireo target (esh)
 -j                  Run the tests against the JavaScript target (vireo.js)
 -t [test suite]     Run the tests in the given test suite
 -l [test suite]     List the tests that would be run in given test suite,
                        or list the test suite options if not provided
 -e                  Execute the test suite or tests provided and show their
                        raw output; do not compute pass/fail
 -h                  Print this usage message
 --once              Will only run the tests once (default is to run twice)
```

## Adding Tests

#### Test Configuration
The `.via` test files are put in the `test-it` folder and the results from the stdout of the test `.via` file from vireo is put in a file of the same name inside the `test-it/results/` folder. The test name is then added to a test suite within the `testList.json` file in the `test-it` directory.

The `testList.json` file has two properties that are required for each test suite name:

##### include
This is an array of strings that are names to other test suites. These test suite names are processed recursively to add the other tests together into one list of tests to run. (Duplicates are omitted if overlaps exists between tests)

##### tests
This is an array of strings that contain the list of `.via` files that the test suite should run.

#### Adding a Test Suite
This will be a simple example that will add the test suite `rpi` with the `RpiTest.via` file to the test manager.

1. Put the `RpiTest.via` in the `test-it/` folder and put the `RpiTest.vtr` file in the `test-it/results/` folder.
2. Then to add the `rpi` test suite to the testing manager, add this example code to the `testList.json` file:
```json
"rpi": {
    "include": [ "common" ],
    "tests": [
        "RpiTest.via"
    ]
}
```
This will add a test suite `rpi` that will include the test `RpiTest.via` and all of the tests included in the `common` test suite.
3. Try it out to verify it all works and your tests pass:
```shell
$ ./test.js -n -t rpi
```

## Running the HTTP test server

### Overview
Vireo utilizes [httpbin](https://httpbin.org/) for testing the HTTP Client functionality. The tests rely on a locally running instance of the httpbin server. The test suite will skip tests (without failing) that rely on the httpbin server running locally if the server is not running. However, if you would like to run the HTTP client tests locally these instructions will help you get configured.

### Requirements
- Python 2.7.9 or later (primarily for pip availability)
- Pip package manager

### Setup
1. Ensure that python (correct version) and pip are available on the path
2. From a command line in the VireoSDK directory or elsewhere do `pip install tox` to globally install the [tox](http://tox.readthedocs.io/en/latest/) tool

### Starting the Server
1. Open a command prompt in the VireoSDK directory
2. Run the `npm run httpbin` command. This will install dependencies of httpbin if necessary and start the httpbin server locally.

   Note: On Windows you can alternatively execute `npm run httpbin-start` to start the httpbin server in a new console window.
3. With the server running in a new window now you can run the tests that rely on the HTTP client (ie `npm run karma` and `make testhttpbin`)

# Updating Vireo Documentation
We are using the [Doxygen](http://www.stack.nl/~dimitri/doxygen/) tool to generate our documentation. The tool allows to annotate our source code and generate documents from there. We are currently using version *1.8.6*.

Installers can be found [here](http://www.stack.nl/~dimitri/doxygen/download.html). On Windows use the 64-bit version.

Once Doxygen is installed just run the following command from any directory in the repo

*`npm run doxygen`*

It will find and use the Doxyfile file in the source directory and will generate the documentation files in the following directory

*`gh-pages`*
	
The main html file in the `gh-pages` is called: index.html

# Legal
Features beyond that core set, that can be accessed directly from VIA source written by hand, should be considered experimental, and subject to change at any time. A complete list of disclaimers and terms is described in LICENSE.txt
