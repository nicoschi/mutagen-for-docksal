# Mutagen for Docksal

A custom global command for [Docksal](https://docs.docksal.io/) to create and manage a Docksal project with a [Mutagen](https://mutagen.io/documentation/introduction/installation) configuration to boost up performances.

## Requirements

- [Docksal](https://github.com/docksal/docksal)
- [Mutagen](https://github.com/mutagen-io/mutagen)

## Installation

Downlaod Mutagen command inside global Docksal commands and make it executable: 

```shell
cd ~/.docksal/commands/
curl https://raw.githubusercontent.com/nicoschi/mutagen-for-docksal/master/mutagen
chmd +x mutagen
```

## Usage

`fin mutagen [command]`

```text
Commands:

start               Start / initialize and start / convert and start 
                    a Docksal project with a Mutagen daemon
 
stop [options]      Stop Docksal project and terminate Mutagen (if already existing)
    -nr             Do not remove cli container (automatically recreated on start)
    
restart [options]   Restart Docksal project and terminate Mutagen (if already existing)
    -nr             Do not remove cli container (automatically recreated on start)
```

### Start

Start command will: 

- **create** a new Docksal project with a Mutagen configuration and start it
- if a Docksal project already exist, **convert** it in a project with Mutagen configuration and **start it**. **A warning will be displayed before proceed because this will recreate docksal containers causing possible data loss (ie.: database tables). So backup data before using it.**
- **simply start** a Docksal project already using a Mutagen configuration previously created with start command

### Stop and restart

A Docksal project using Mutagen will use `DOCKSAL_VOLUMES="none"` to speed up performances as stated in [Docksal Shared Volumes Documentation](https://docs.docksal.io/core/volumes/). 

With this option enabled the codebase on the host has to be synced inside the cli container (action which Mutagen will take care of) resulting in higher disk space usage (double the size of the codebase). 

So `stop` and `restart` command by default will delete the cli container (automatically recreated on start) to preserve disk space during project inactivity. 

If you don't want destroy and recreate the cli container on stop and restart use: 

- `stop -nr`
- `restart -nr`

## Mutagen default configuration

This is the default Mutagen configuration created in the root of the Docksal project you can customize after creation:

```yaml
sync:
  defaults:
    flushOnCreate: true
    ignore:
      vcs: true
    permissions:
      defaultFileMode: 644
      defaultDirectoryMode: 755
  code:
    alpha: 'alphadir'
    beta: 'betadir'
    mode: 'two-way-resolved'
```

After configuration creation: 

- `alphadir` will be repleced by the docroot relative path of the Docksal project
- `betadir` will be replaced by `docker://docker@{project_name}_cli_1/var/www/{docroot}` where `{project_name}` and `{docroot}` are respectively the Docksal project name and docroot.

## Docksal Configuration Variables

On project creation inside the `./docksal/docksal.env` will be created some new configurations variables: 

- `MUTAGEN="1"` indicating the project is using Mutagen
- `PROJECT_ROOT="/absolute/host/path/to/docksal/project"`
- `PROJECT_NAME="mutagen-for-docksal"`
