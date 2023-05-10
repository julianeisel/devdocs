<!-- Auto-conversion test using pandoc, no edits -->

New Developer Info {#new_developer_info}
==================

```{=mediawiki}
{{Template:Lead|
This page is meant to provide new developers with information about how the Blender development process works, where to find info and how to find a good project to work on.
}}
```
Communication
-------------

There are various communication channels used.

-   [**BF-Committers Mailing
    List**](http://lists.blender.org/mailman/listinfo/bf-committers) -
    for public discussions, where official decisions are made.
-   [**Issue Tracker**](https://projects.blender.org/) - for bugs, code
    contributions and managing design tasks.
-   [**Blender Chat**](https://blender.chat/) -
    [\#blender-coders](https://blender.chat/channel/blender-coders) for
    informal conversation and discussions with/between developers.
-   [**DevTalk Forum**](https://devtalk.blender.org) - for general
    developer discussion & questions.

See the [Contact](Contact "wikilink") page for more information.

If you want to start developing in Blender you will have lots of startup
questions. You can ask them in the \#blender-coders blender.chat channel
each day of the week.

When in doubt and no particular channel seems to match your question,
mail the [bf-committers mailing
list](http://lists.blender.org/mailman/listinfo/bf-committers) and you
will get an answer there or get redirected to the right channel.

No spamming or flaming on the lists or other channels is allowed. Please
stay on topic in the lists, and follow good net etiquette. Also do not
send html messages to the mailing lists. Html messages can be contain a
variety of security problems and as such tend to be mistrusted by many
people.

Module Meetings {#module_meetings}
---------------

The modules have autonomy to organize their work how they see fit. Some
modules have weekly or bi-weekly meetings. The less active modules meet
once there is something specific to discuss. The future meetings are
announced as part of the [meeting
notes](https://devtalk.blender.org/tag/meeting).

Getting Started {#getting_started}
---------------

#### Building Blender {#building_blender}

See [Building Blender](Building_Blender "wikilink").

#### Decisions

Blender development is structured similarly to Python\'s BDFL model
(BDFL stands for [Benevolent Dictator For
Life](http://en.wikipedia.org/wiki/Benevolent_Dictator_for_Life)). The
idea is that the project is managed by one person, in this case Ton
Roosendaal, who delegates design decisions to a group of [module
owners](Modules "wikilink"), while still maintaining a veto right of
what goes into Blender.

#### Your First Project {#your_first_project}

Once you have blender compiled you\'re probably wondering where to start
tinkering. If you are a Blender user then there is probably some
niggling thing you would like to add or change. If you are new to
Blender then you will need to take a look and see what you might work
on.

There are no hard and fast rules about where to start, so find an area
that interests you and start looking onto it.

A good location to start checking on what is on the roadmaps for modules
is the list on the front page of
[projects.blender.org](https://projects.blender.org/).

Some hints for your first projects:

-   An area of interest (even better, an area of Blender you use)
-   Double check it is not already done.
-   Try to avoid a project that spans across many files and areas of
    Blender, since you may get bogged down trying to understand
    everything at once.

Here are some areas that can give you a foot in the door:

##### Small Features {#small_features}

Adding in small features is a good way to start getting your hands
dirty, even if you don\'t contribute the changes. The feature you worked
on can evolve into something more useful, take a new direction, or spark
interest in new areas to develop.

[Here is a list of quick
hacks](https://projects.blender.org/blender/blender/issues?labels=302) -
tasks that core developers think could be reasonably tackled as a first
blender project without a major time commitment (these tasks typically
would take a core developer less than 4 hours to accomplish, but might
take quite a bit more time for a new coder who needs to learn the code
base).

##### Bug Fixing {#bug_fixing}

One of the easiest ways to get involved with coding Blender is by fixing
bugs. Bug-fixing lets you focus on a small part of the Blender source
rather than trying to understand it all at once. The list of current
bugs is on [projects.blender.org](http://projects.blender.org). So pick
an area of interest and start fixing! When you have got it, [make a pull
request](Tools/CodeReview "wikilink").

Navigating the Code {#navigating_the_code}
-------------------

Have a look at the [Files structure](Source/File_Structure "wikilink")
and [Code Layout](http://www.blender.org/bf/codelayout.jpg) diagram.

The editors directory is usually a good place - it is where most of the
operators live. Have a look at the header files and structs related to
what you are interested in working on. The headers usually have the best
overview of what a function does. To find the struct a simple grep or
other search for struct Foo.

You can also start with writing python scripts, the [API for our python
tools](https://docs.blender.org/api/master/) - is similar in many ways
to our C API. You can often find out where some C code lives by seeing
the python tool tips by hovering over a button and seeing what the
operator name is. If you add a console window you can see what is output
to it when you do an action, then just search the code.

Also putting a break on a function in a debugger and doing a back trace
can help you find the path code took to get to your function of
interest. Or you can start blender from the command line with the -d
option and every command is printed to the console.

Debugging
---------

Have a look at [Debugging](Tools/Debugging "wikilink") for help and
hints on doing debugging. If you end up not being able to solve the bug
yourself, post a bug to our [Bug
Tracker](https://projects.blender.org/blender/Blender/issues), if it is
a bug in our code.

If it is a bug in your code, load your test file and the information you
have found out about the bug to [pasteall.org](http://www.pasteall.org)
then ask for help in
[\#blender-coders](https://blender.chat/channel/blender-coders) on
blender.chat. If no-one can help you there you can try emailing
[bf-committers\@blender.org](http://lists.blender.org/mailman/listinfo/bf-committers).

Development Process {#development_process}
-------------------

Steps for getting a change into Blender

1.  Find an area in Blender you want to improve upon.
2.  If it\'s a non-trivial feature, check in advance if Blender
    developers agree with the project and design, to avoid doing work
    for nothing. Contacting developers is typically done on the
    [developer forums](https://devtalk.blender.org), or on
    [\#blender-coders](https://blender.chat/channel/blender-coders) on
    blender.chat. To talk to the individual owners of parts of the code,
    see the list of [module owners](Modules "wikilink")
3.  Do the actual coding :)
4.  Once you have code, see the [Contributing
    Code](Process/Contributing_Code "wikilink") section on how to [make
    a pull request](Tools/CodeReview "wikilink") get it submitted,
    reviewed and included.

Technical Documentation {#technical_documentation}
-----------------------

We encourage everyone to use the [Blender Wiki](http://wiki.blender.org)
to document code work and to share proposals. Note that we strictly
apply the rule \"No code gets in trunk without documentation!\". Also
never hesitate to include good example .blend files and tests and videos
showing the feature.

People who only want to help with technical documentation are welcome
too. Just use the general contact information here to get started.

Release Process {#release_process}
---------------

For more information about the release schedule, see [Release
Cycle](Process/Release_Cycle "wikilink") and [Current
Projects](Dev:Projects "wikilink") to understand the schedule and times
when new features can get in and releases are made.
