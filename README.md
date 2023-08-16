# UDMI Site Model

This repo provides a sample/template
[UDMI site model](https://github.com/faucetsdn/udmi/blob/master/docs/specs/site_model.md).
It is used by the UDMI CI suite, and can also be used a base template for creating
a new site model.

Top level structure:

* `cloud_iot_config.json`:
* `devices/`: Devices for this site, or devices under test
* `expected/`: Expected sequencer outputs for CI automation runs
* `README.md`: This file
* `reflector/`: Configuration used for reflector (MQTT) access to a hosted UDMIS install
* `site_metadata.json`: Default metadata applied to _all_ devices
* `.gitignore`: Standard git housekeeping
* `.github/workflows/testing.yml`: GitHub Actions CI automation workflow

## CI Automation

_Note that a variable is considered **empty** if it is not defined at all in GitHub Actions, while a variable
defined as a space is considered **blank**. This is important for enabling some of the
optional steps below. GitHub Actions will not allow you to define an empty varaible. The term 'not-empty' used
below means that it is defined in GitHub Actions, but may be defined as blank whitespace._

GitHub Actiona Variables _or Secret as indicated_: (listed in order of use, optional unless indicated otherwise)
* `UDMI_REPO` (required): Git repo to clone for UDMI tooling, typically `https://github.com/grafnu/udmi.git`
* `UDMI_VERSION` (required): Version to use from indicated repo, either tagged (`1.4.1`) or a branch (`master`)
* `PROJECT_ID` (required): Project hosting UDMIS install to use, with provider prefix, e.g. `//clearblade/udmi-external`
* `SITE_MODEL` (required): Location of the UDMI site model (where the `cloud_iot_config.json` file is) relative to the git root,
   e.g. `.` for the root directory (like the `udmi_site_model` example), or maybe `udmi` if it's in a subdirectory
* `REFLECTOR_PKCS8_B64` (secret): Base-64 encoded private key for the UDMI-Reflect registry. Encoded with something like
  `$ base64 reflector/rsa_private.pkcs8`. If not-empty, the key will need to be present in the repo itself (not as secure).
* `UPDATE_OPTS`: If not-empty, this will trigger an automatic update of the UDMIS install in the target project.
  Currently the contents of this variable are not used.
* `KEYGEN_DEVICE`: If not-empty, the system will dynamically generate a new key for the indicated device. If
  site registration is enabled, this key will be used instead of whatever might be in the repo. This isn't necessarily the
  same as `DEVICE_NAME` since the key might be associated with a gateway rather than the device-under-test itself.
* `SITE_OPTS`: If not-empty, the system will run the `registrar` tool on the site, registering all the devices
  in the site model. The `SITE_OPTS` value itself is passed direct to the registrar tool, so can be used for any specific
  customizations (or it can be blank with no effect). If site-opts is empty, then ie system will register just the device
  indicated by `DEVICE_NAME`.
* `DEVICE_NAME`: Specific individual device to use for single-device registration, sequence testing, and optional pubber
  invocation.
* `PUBBER_OPTS`: If not-empty, will trigger pubber for virual testing (or leave empty for no pubber invocation). If not-empty,
  it will be passed as an argument to the pubber tool to specify any requistie options.
* `DEVICE_SERIAL`: The serial number to use for the device-under-test (argument to pubber and sequencer tool).
* `PUBBER_PKCS8_B64` (secret): Base-64 encoded private key for pubber to use, in case it needs to match a private key for an
  actual device. Alternatively, the `KEYGEN_DEVICE` field above can be used to dynamically generate a key.
* `VALIDATOR_OPTS`: If not-empty, will trigger a udmi validator run on the model. The value will be passed as command line
  arguments to the validator tool.
* `VALIDATOR_TIMEOUT`: The time dureation for running the validator tool (e.g. `20m`). This will default to `10m` if not not-empty.
* `SEQUENCER_OPTS`: If not-empty, will trigger a sequencer run test on the `DEVICE_NAME` device. The value itself will be passed
  as a command-line argument to the sequencer tool.
* `SEQUENCER_TESTS`: A list of specific tests to run for sequencer, passed as a command-line argument to the tool.
