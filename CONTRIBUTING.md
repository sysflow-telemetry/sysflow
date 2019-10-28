## Contributing In General
Our project welcomes external contributions. 

To contribute code or documentation, please submit a pull request to the proper github repositories.

A good way to familiarize yourself with the codebase and contribution process is
to look for and tackle low-hanging fruit in the github issue trackers associated with projects.
Before embarking on a more ambitious contribution, please quickly [get in touch](#communication) with us.

**Note: We appreciate your effort, and want to avoid a situation where a contribution
requires extensive rework (by you or by us), sits in backlog for a long time, or
cannot be accepted at all!**

### Proposing new features

If you would like to implement a new feature, please raise an issue in the appropriate repository
before sending a pull request so the feature can be discussed. This is to avoid
you wasting your valuable time working on a feature that the project developers
are not interested in accepting into the code base.

### Fixing bugs

If you would like to fix a bug, please raise an issue in the appropriate repository before sending a
pull request so it can be tracked.

### Merge approval

The project maintainers use LGTM (Looks Good To Me) in comments on the code
review to indicate acceptance. A change requires LGTMs from two of the
maintainers of each component affected.

For a list of the maintainers, see the MAINTAINERS.md page in the appropriate repository.

## Legal

Each source file must include a license header for the Apache
Software License 2.0. Using the SPDX format is the simplest approach.
e.g.

```
/*
Copyright <holder> All Rights Reserved.

SPDX-License-Identifier: Apache-2.0
*/
```

We have tried to make it as easy as possible to make contributions. This
applies to how we handle the legal aspects of contribution. We use the
same approach - the [Developer's Certificate of Origin 1.1 (DCO)](https://github.com/hyperledger/fabric/blob/master/docs/source/DCO1.1.txt) - that the Linux® Kernel [community](https://elinux.org/Developer_Certificate_Of_Origin)
uses to manage code contributions.

We simply ask that when submitting a patch for review, the developer
must include a sign-off statement in the commit message.

Here is an example Signed-off-by line, which indicates that the
submitter accepts the DCO:

```
Signed-off-by: John Doe <john.doe@example.com>
```

You can include this automatically when you commit a change to your
local git repository using the following command:

```
git commit -s
```

## Communication
Please feel free to connect with us on our [Slack channel](https://join.slack.com/t/sysflow-telemetry/shared_invite/enQtODA5OTA3NjE0MTAzLTlkMGJlZDQzYTc3MzhjMzUwNDExNmYyNWY0NWIwODNjYmRhYWEwNGU0ZmFkNGQ2NzVmYjYxMWFjYTM1MzA5YWQ) or
via [email](mailto:sysflow@us.ibm.com). Note that the projects in this repository are not formal products. As a result, the communication channels are to the maintainers and not to a support staff.

## Setup

The documentation is a work in progress but should provide a good overview on how to get started with the project.  The Dockerfile also provides a treasure trove of information
on how to build the application, dependencies, and how to test the collector. 

## Testing

This project is in its infancy and with limited resources we haven't built many testers for the projects.  For the sf-collector, we do have a set of unit tests that test the coverage of most of the events of interest in `sf-collector/tests`.   
These tests can be run using the [bats testing framework](https://github.com/bats-core/bats-core).   Directions on how to install bats are in the accompanied link.    To run the tests, run `bats -t tests.bat` from the tests directory.  Note,
that the tests also rely on python3. Before conducting a pull request, these unit tests should be run.  Note, there is a version of the docker image with a `testing` tag that contains bats and the unit tests.   This might be useful for testing.
Also, conducting a load test and running the application under valgrind is desirable for pull requests. 


## Coding style guidelines
We follow the [LLVM Coding standards](https://llvm.org/docs/CodingStandards.html) where possible across the projects.   There is a .clang-format file in the master repo [clang-format](https://github.com/sysflow-telemetry/sf-collector/blob/master/src/.clang-format) that can be used in conjunction with [ClangFormat Tool](https://clang.llvm.org/docs/ClangFormat.html) to automatically format code. For linting,
we use [Clang Tidy Linter](https://clang.llvm.org/extra/clang-tidy/).  This is referenced in the sf-collector Makefile.
