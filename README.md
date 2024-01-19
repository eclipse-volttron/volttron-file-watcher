# volttron-file-watcher

The File Watch Publisher agent watches files listed in its configuration for changes.  The agent will detect changes to
those files and publish those changes line-by-line on the topic the user has associated with the file in the
configuration.

The user should be careful about what files are being watched, and which historians are being used with the
File Watch Publisher.  Very long lines being output in individual messages on the message bus can result in some
performance degradation.  Some configurations of the File Watch Publisher can affect the system (such as using I/O
resources when a fast-moving log is being captured in a SQLite Historian), so the user should be intentional about which
files the agent is configured to watch and the topics used for publishes.

## Prerequisites

* Python 3.10

## Python

<details>
<summary>To install Python 3.10, we recommend using <a href="https://github.com/pyenv/pyenv"><code>pyenv</code></a>.</summary>

```bash
# install pyenv
git clone https://github.com/pyenv/pyenv ~/.pyenv

# setup pyenv (you should also put these three lines in .bashrc or similar)
export PATH="${HOME}/.pyenv/bin:${PATH}"
export PYENV_ROOT="${HOME}/.pyenv"
eval "$(pyenv init -)"

# install Python 3.10
pyenv install 3.10

# make it available globally
pyenv global system 3.10
```

</details>

## Installation

1. Create and activate a virtual environment.

```shell
python -m venv env
source env/bin/activate
```

2. Install volttron and start the platform.

```shell
pip install volttron

# Start platform with output going to volttron.log
volttron -vv -l volttron.log &
```

3. Create configuration.

Create a config directory and navigate to it:

```shell
mkdir config
cd config
```

Navigate to the config directory and create a file called `filewatcher.config` then use the below example to populate this file with the correct JSON.

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

4. Install the file watcher agent

```bash
vctl install volttron-file-watcher --agent-config filewatcher.config --vip-identity platform.file_watcher --start --force
```

5. Observe Data

Once installed, you should see the data being published by viewing the Volttron logs file that was created in step 2.
To watch the logs, open a separate terminal and run the following command:

```bash
tail -f <path to folder containing volttron.log>/volttron.log
```

### Example Publish

The following is an example publish by the File Watch Publisher installed with the above configuration.

```log
Topic: record/myservice/logs
Headers: {'min_compatible_version': '3.0', 'max_compatible_version': ''}
Message: {'timestamp': '2024-01-06T00:07:09.370074Z', 'line': '2024-01-05 16:07:09,358 (volttron-file-watcher-0.1.0 63487) volttron.client.vip.agent.core(527) DEBUG: address: ipc://@/home/riley/volttron-file-watcher/volttron_home/run/vip.socket'}
```

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
