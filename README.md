# volttron-file-watcher

The File Watch Publisher agent watches files listed in its configuration for changes.  The agent will detect changes to
those files and publish those changes line-by-line on the topic the user has associated with the file in the
configuration.

The user should be careful about what files are being watched, and which historians are being used with the
File Watch Publisher.  Very long lines being output in individual messages on the message bus can result in some
performance degradation.  Some configurations of the File Watch Publisher can affect the system (such as using I/O
resources when a fast-moving log is being captured in a SQLite Historian), so the user should be intentional about which
files the agent is configured to watch and the topics used for publishes.

## Requires

* python >= 3.10
* volttron >= 10.0

## Installation

Before installing, VOLTTRON should be installed and running.  Its virtual environment should be active.
Information on how to install of the VOLTTRON platform can be found
[here](https://github.com/eclipse-volttron/volttron-core).

Create a directory named `config` and use the change directory command to enter it.

```shell
mkdir config
cd config
```

After entering the config directory, create a file named `file_watcher_config.json`, use the below JSON to populate your new file.

## Example Usage

The user wants to record logging information from the "myservice" service into a historian agent.

The user can configure the File Watch Publisher to point at the "myservice.log" file with a corresponding "record"
topic -  for example "record/myservice/logs".  As "myservice" adds logging entries to its log file, the File Watch
Publisher will capture each new log message and publish it to the "record/myservice/logs" topics on the message bus.

Below is a File Watch Publisher example configuration to match the above scenario.

### Configuration

```json
{
    "files": [
        {
            "file": "/opt/myservice/logs/myservice.log",
            "topic": "record/myservice/logs"
        }
    ]
}
```

Install the file watcher agent

```bash
vctl install volttron-file-watcher --agent-config file_watcher_config --vip-identity platform.file_watcher --start --force
```

View the status of the installed agent.

```shell
vctl status
```

## Development

Please see the following for contributing guidelines [contributing](https://github.com/eclipse-volttron/volttron-core/blob/develop/CONTRIBUTING.md).

Please see the following helpful guide about [developing modular VOLTTRON agents](https://github.com/eclipse-volttron/volttron-core/blob/develop/DEVELOPING_ON_MODULAR.md)

## Disclaimer Notice

This material was prepared as an account of work sponsored by an agency of the
United States Government.  Neither the United States Government nor the United
States Department of Energy, nor Battelle, nor any of their employees, nor any
jurisdiction or organization that has cooperated in the development of these
materials, makes any warranty, express or implied, or assumes any legal
liability or responsibility for the accuracy, completeness, or usefulness or any
information, apparatus, product, software, or process disclosed, or represents
that its use would not infringe privately owned rights.

Reference herein to any specific commercial product, process, or service by
trade name, trademark, manufacturer, or otherwise does not necessarily
constitute or imply its endorsement, recommendation, or favoring by the United
States Government or any agency thereof, or Battelle Memorial Institute. The
views and opinions of authors expressed herein do not necessarily state or
reflect those of the United States Government or any agency thereof.
