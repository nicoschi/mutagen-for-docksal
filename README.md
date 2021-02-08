# Mutagen for Docksal

A custom global command for [Docksal](https://docs.docksal.io/) to create and manage a [Mutagen project](https://mutagen.io/documentation/orchestration/projects) inside a Docksal one to boost up underlying Docker filesystem performances on MacOS and Windows.

## Requirements

- [Docksal](https://github.com/docksal/docksal): tested on fin 1.103.0
- [Mutagen](https://github.com/mutagen-io/mutagen): tested on 0.11.8

## Installation

Downlaod `mutagen` command inside global Docksal commands and make it executable: 

```shell
cd ~/.docksal/commands/
curl https://raw.githubusercontent.com/nicoschi/mutagen-for-docksal/master/mutagen > mutagen
chmod +x mutagen
```

## Usage

In a Docksal project launch `fin mutagen [command]`. 

If no Docksal project exists you must launch it inside the folder you want to create a Docksal project with Mutagen, otherwise you can launch it inside Docksal project root or any of its subfolders.

```text
Commands:

start               Start / initialize and start / convert and start 
                    a Docksal project alongside with a Mutagen project
 
stop [options]      Stop Docksal project and terminate Mutagen project (if already existing)
    -nr             Do not remove cli container (automatically recreated on start)
    
restart [options]   Restart Docksal project and Mutagen project (if already existing)
    -nr             Do not remove cli container (automatically recreated on start)
```

### Start

Start command will: 

- **create** a new Docksal project alongside with a Mutagen project configuration and start them
- if a Docksal project already exist, **convert** it in a project with a Mutagen project configuration **and start it**. 
  **A warning will be displayed before proceed because this will recreate docksal containers causing possible data loss (ie.: database tables). So backup data before using it.**
- **simply start** a Docksal project already using a Mutagen project configuration previously created with start command

### Stop and restart

**Stop** or **restart** a Docksal project alongside with Mutagen project **and delete the cli container** (automatically recreated on start) to preserve disk space during project inactivity.

**If you don't want** to delete the cli container on stop and restart **use**:

- `stop -nr`
- `restart -nr`

The default setting is due to the fact that a Docksal project which uses Mutagen will use `DOCKSAL_VOLUMES="none"` to speed up filesystem performances as stated in [Docksal Shared Volumes Documentation](https://docs.docksal.io/core/volumes/). 

With this option enabled the codebase on the host has to be mirrored and continuously synced inside the cli container (action which Mutagen will take care of) resulting in higher disk space usage (double the size of the codebase).

## Mutagen default configuration

This is the default Mutagen project configuration will be created in the root of the Docksal project on first start and you can customize later:

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
- `PROJECT_NAME="project_name"` coinciding with docksal project root folder name

## Further readings

- [Docker containers file system performance under different scenarios](https://github.com/docksal/docksal/issues/249)
- [Why is Docker on macOS So Much Worse Than Linux? ](https://dev.to/ericnograles/why-is-docker-on-macos-so-much-worse-than-linux-flh)
- [Shared Volumes](https://docs.docksal.io/core/volumes/)
- [Docksal Custom Commands](https://docs.docksal.io/fin/custom-commands)

