# MistDropCLI

Do you have DigitalOcean droplets that you use infrequently? Do you want to avoid paying for them
all month but still have them ready at a moment's notice? Do you want to be able to treat them as
a service that you can easily start/stop from the command line?

You should try using MistDrops.

MistDropCLI is a python command line tool that uses DigitalOcean's API to bring droplets up from
snapshots when you want to use them, and convert them back into snapshots when you don't need
them any more. It's simple and easy to use.

## Warnings

MistDropCLI is intended to "just work", without you having to worry about whether you're going to
accidentally lose a droplet. However, it does destroy specific droplets and snapshots as part of
its operation, so you should understand how it behaves before you use it.

MistDropCLI can output monthly cost estimates based on current droplets and snapshots. This is
only intended as a guideline based on your DigitalOcean account's current state. It is not
authoritative, and should in no way be used as a substitute for DigitalOcean's billing UI.

## What is a "MistDrop"?

A MistDrop is one or more snapshots and (optionally) one droplet whose names all start with the
same _prefix string_. The MistDrop is up if a droplet matching the prefix string exists, and it's
down if no such droplet exists. 

## Installation

* Make sure you have python 2.X installed.
* pip install python-digitalocean
* Download mistdrop
* If you don't have one, generate an API token in DigitalOcean.
* Create .access\_token file in the mistdrop directory, and save your API token there.

## Usage

```
mistdrop status
```

Lists all droplets and snapshots for the given DigitalOcean account, along with estimated monthly
cost if the state of droplets/snapshots doesn't change. Droplet costs are based on DigitalOcean's
monthly costs as stated in their API, increased by 20% if a particular droplet has backups
enabled. Snapshot cost is calculated based on snapshot storage size at $0.05 per GB per month.

```
mistdrop status [mistdrop-name]
```

Same as `mistdrop status`, but restricts results and cost calculations to droplets and snapshots
whose name starts with `mistdrop-name`.

```
mistdrop up [mistdrop-name]
```

Create a new droplet called `mistdrop-name`, using the most recently created snapshot whose name
starts with `mistdrop-name`.

* If a droplet whose name starts with `mistdrop-name` already exists, ensure it's powered up rather than creating a new one.
* Complains and refuse to do anything if:
  * No matching snapshots exist
  * More than one matching droplet exists, or
  * One matching droplet exists and is locked from changes (i.e. is in the middle of being created or destroyed)

```
mistdrop down [mistdrop-name]
```

Create a new snapshot from an existing droplet matching `mistdrop-name`, powering it down first
if necessary. Once the snapshot is created, *destroy* any older snapshots matching `mistdrop-name`,
and then destroy the droplet.

* When powering down a droplet, MistDropCLI will start with a gentle shutdown command, and then move on to the harsher power-off command if the gentle version takes too long.
* Complains and refuses to do anything if:
  * No matching droplet exists
  * More than one matching droplet exists
  * The existing droplet is locked from changes (i.e. is in the middle of being created or destroyed)
* Will halt prior to destroying snapshots/droplet if it can't find the snapshot it told DigitalOcean to create.

## Development Roadmap

* Read configuration (access token, created droplet size, region, timeouts, etc) from a generated JSON file
* Retain N snapshots rather than just 1
* Allow use of tags rather than matching the beginning of droplet/snapshot names
* MistDrop-specific overrides in JSON config 
* Proper packaging and installation via pip
* Support N droplets for a given MistDrop
* Provide more examples in documentation

## Licensing

MistDropCLI is released under the MIT license. Enjoy!
