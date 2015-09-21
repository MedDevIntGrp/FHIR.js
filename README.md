# FHIR.js
Node.JS library for serializing/deserializing FHIR resources between JS/JSON and XML using various node.js XML libraries

![Build Status](https://travis-ci.org/lantanagroup/FHIR.js.svg?branch=master)

# Dependencies
* q 1.4.1
* xml2js 0.4.9
* xmlbuilder 2.6.4
* libxmljs 0.14.3

# Installation
```
npm install fhir
```

# Documentation
## ctor(version)
Indicate which version of FHIR the module should work with. Only DSTU1 is currently supported. If no version is specified, defaults to DSTU1.

```
var FHIR = require('fhir');
var fhir = new FHIR(FHIR.DSTU1);
```
## ObjectToXml
Converts a JS object to FHIR XML.

```
var FHIR = require('fhir');
var myPatient = {
  resourceType: 'Patient',
  ...
};
var fhir = new FHIR(FHIR.DSTU1);
var xml = fhir.ObjectToXml(myPatient);
```
## JsonToXml
Converts JSON to FHIR XML.

```
var FHIR = require('fhir');
var myPatient = {
  resourceType: 'Patient',
  ...
};
var myPatientJson = JSON.stringify(myPatient);
var fhir = new FHIR(FHIR.DSTU1);
var xml = fhir.JsonToXml(myPatientJson);
```
## XmlToJson
Converts FHIR XML to JSON. Wrapper for XmlToObject that parses JSON into an object and returns the object

```
var FHIR = require('fhir');
var myPatientXml = '<Patient xmlns="http://hl7.org/fhir"><name><use value="official"/><family value="Chalmers"/><given value="Peter"/><given value="James"/></name></Patient>';
var fhir = new FHIR(FHIR.DSTU1);
fhir.XmlToJson(myPatientXml)
    .then(function(myPatientJson) {
        // Do something with myPatientJson
    })
    .catch(function(err) {
        // Do something with err
    });
```
## XmlToObject
Converts FHIR XML to JS object

```
var FHIR = require('fhir');
var myPatientXml = '<Patient xmlns="http://hl7.org/fhir"><name><use value="official"/><family value="Chalmers"/><given value="Peter"/><given value="James"/></name></Patient>';
var fhir = new FHIR(FHIR.DSTU1);
fhir.XmlToObject(myPatientXml)
    .then(function(myPatient) {
        // Do something with myPatient
    })
    .catch(function(err) {
        // Do something with err
    });
```

## ValidateXMLResource
Validates the XML resource against the FHIR schemas

```
var bundleXml = fs.readFileSync('./test/data/bundle.xml').toString('utf8');
var fhir = new Fhir(Fhir.DSTU1);
var result = fhir.ValidateXMLResource(bundleXml);

// result is an object that contains "valid" (true | false) and "errors" (an array of string errors when valid is false).
```

## ValidateJSResource
Validates the specified JS resource against a profile. If no profile is specified, the base profile for the resource type will be validated against.

```
var compositionJson = fs.readFileSync('./test/data/composition.json').toString('utf8');
var composition = JSON.parse(compositionJson);
var fhir = new Fhir(Fhir.DSTU1);
var result = fhir.ValidateJSResource(composition);
```

## ValidateJSONResource
Validates the specified JSON resource against a profile. If no profile is specified, the base profile for the resource type will be validated against.

```
var bundleJson = fs.readFileSync('./test/data/bundle.json').toString('utf8');
var bundle = JSON.parse(bundleJson);
var fhir = new Fhir(Fhir.DSTU1);
var result = fhir.ValidateJSResource(bundle);
```

# Implementation Notes
* **FHIR DSTU2 is not yet supported**
* Feeds and resources are both supported for DSTU1. There is no need to do anything different for converting feeds vs. resources. The library will automatically detect what should be produced based on the resourceType of the JS object passed, or based on the root element of the XML.
* Unit tests use samples pulled from the FHIR standard. Mocha is used to execute the unit tests. Either execute "mocha test" or "npm test" to run the unit tests
* libxmljs is used to validate XML against the FHIR schemas
* xml2js is used to parse XML and create JS
* xmlbuilder is used to create XML from JS
* FHIR profiles (within the "profiles" directory) are used to determine whether properties should be arrays. The profiles are loaded into memory whenever an instance of the FHIR module is created. This could be improved to reduce I/O operations...