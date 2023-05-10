<!-- Auto-conversion test using pandoc, no edits -->

Advice for New Developers {#advice_for_new_developers}
=========================

### Preface

Every so often we have emails on the Blender mailing list with subjects
like: *\"I\'d like to get involved with Blender development\"*.

This page is intended as a reply from active developers, giving
practical advice to anyone who\'s new to Blender development and wants
to get involved.

While there is no shortage of technical documentation for nearly every
aspect of development,\
this page focuses on how to approach taking your first steps.

### Get Blender Building {#get_blender_building}

Before thinking ahead too much and asking lots of questions about the
code - just get Blender building.

See: [Building Blender](Building_Blender "wikilink").

### Setup a Development Environment {#setup_a_development_environment}

While a lot could be written about editors and IDE\'s and editors, here
are some suggestions:

-   Using any programmers text editor with a search tool (even grep), is
    fine for C or Python development.
-   You may want to use different editors, one for Python, another for
    C/C++.\
    *(since we use different indentation settings, some edits such as
    Vim/Emacs allow per-filetype indentation configuration)*
-   IDE\'s are more useful for C++ code, especially areas that use class
    inheritance since it\'s often not so obvious which members are
    available.
-   *Some IDE\'s may have trouble with Blender!*\
    Blender source code is large enough that some IDE\'s are unusably
    slow.\
    While not wanting to promote specific technologies, here\'s a
    short-list of IDE\'s known to work well for Blender development.
    -   For Windows: Visual Studio MSVC is a popular choice.
    -   For OSX: XCode.
    -   For Linux there are a few options\
        VS Code and QtCreator are popular choices, KDevelop is usable
        too, Eclipse & Netbeans are functional but can be slow.

------------------------------------------------------------------------

:   Notes:
    -   *All IDE\'s mentioned support CMake for generating project
        files.*\
    -   *Some Blender developers don\'t use IDE\'s at all, with
        experience you may want use a development environment not
        mentioned here.\
        The list above is just suggesting some starting points known to
        work for others.*

#### Initial Development Setup {#initial_development_setup}

Whether you\'ve already chosen your development tools, or you\'re just
getting started, once you can compile the code:

At a **minimum** you must be able to do these operations on the entire
code-base quickly and easily:

-   Edit source code.
-   Lookup any file by name, for editing.
-   Search all text in the source code.\
    *(function names, tool-tips, comments\... etc).*

*If you aren\'t sure how to do any of the operations above, take some
time to investigate how to perform them, otherwise basic navigation of
the source will be unnecessarily difficult.*

See: [Development Environment
Setup](Developer_Intro/Environment "wikilink").

#### Note on Configuration {#note_on_configuration}

At a **minimum** you\'ll need to configure your editor/IDE \...

-   Set indentation to spaces, width 2 for C/C++.
-   Set indentation to spaces, width 4 for Python.

For details see: [Code Style
Configuration](Source/Code_Style/Configuration "wikilink").

### Pick a Project {#pick_a_project}

So you\'re all ready to go, it\'s time to pick some project, this is
difficult to give good *general* advice for since it\'s entirely up to
your needs/interests.\
Having said that, here are some things to consider.

-   In most cases we suggest to start really small and treat it as an
    exercise, your first project may not end up being useful and even
    things you would expect to be *easy* might not be.\
    This also helps you become used to navigating the source code,
    reading it, making edits - and understanding it. While you do this
    you can think of more ambitious/interesting things to change too, so
    this time isn\'t wasted.
-   As for what to work on, feel free to ask around but everyone will
    give different suggestions.
    -   **Add a Feature** - This is often the most fun but before
        spending a lot of time on this it\'s best to find out if the
        feature would even be accepted, some features are intentionally
        not added to Blender because they don\'t fit well with the
        existing design.\
        Look into our [Good First
        Issue](https://projects.blender.org/blender/blender/issues?labels=302)
        page for a list of approved projects, suitable for beginners.
    -   **Improve a Feature** - When using Blender you might run into
        some simple limitation - Lasso select tool didn\'t work on UV
        editor for example, or that the smooth tool doesn\'t work on a
        lattice\
        Making improvements like this is good because you\'re working
        within the current design and there\'s a much higher chance of
        having your work accepted.
    -   **Fix a bug** - take care with this, bug fixes are welcome of
        course but can often be quite complicated to investigate and you
        may end up spending a lot of time and still not find a fix.\
        *Hint, recently reported bugs are often less trouble to
        resolve!*
    -   **Janitor Work** - To get a patch accepted and to get to know
        the code, contact developers and to generally follow the whole
        process; more mundane patches can still help you get involved.
        -   tooltip/spelling corrections.
        -   fixes/improvements to the build-system.
        -   quiet compiler warnings.
        -   installer / packaging improvements.
-   Avoid small patches which only tweak existing behavior or tweaks to
    defaults *(early on at least)*. These kinds of changes are very easy
    for existing developers to make, so it\'s not really all that
    helpful to send such *opinionated* patches.\
    It\'s common for people to come to Blender from other software and
    want to make it work how they like, but it\'s not the purpose of
    Blender to be a clone of another application.\
    At least learn *the Blender way* before trying to make Blender
    behave like some other application, perhaps what you want can be
    done with key-map modifications, or added as a user preference, but
    to start with it\'s best to avoid *controversial* changes.\
    - You risk spending too long on discussions with existing
    maintainers - before you have a good understanding of internal
    workings.
-   For a definitive answer on whether your project might be accepted -
    talk to the developer(s) who would review your work:\
    See: [Module Owners](Template:ModulesOwners "wikilink").

### Communicate with Other Developers {#communicate_with_other_developers}

Normally when you start out you\'ll have lots of questions, not all of
them can be documented - so here are the 3 main ways Blender developers
communicate.

-   Join the [bf-committers mailing
    list](http://lists.blender.org/mailman/listinfo/bf-committers).
-   Join the [Devtalk forums](http://devtalk.blender.org/).
-   Talk with developers and users on
    [\#blender-coders](https://blender.chat/channel/blender-coders).

### Further Suggestions {#further_suggestions}

-   Disable most features when building Blender (OpenVDB, Collada,
    FFMpeg, Cycles, LibMV\... etc) - will speed up rebuilds
    considerably.\
    *Unless you intend to develop on any of these areas of course.*
-   While you will eventually want to use a debugger to step over the
    source,\
    try using `printf()` for debugging, sometimes if you\'re not sure of
    how something works it\'s good to add prints into the code, for your
    own code or even if you read it and want to understand it better,
    this is a very simple/stupid way to check on things but handy, fast
    rebuilds make this less of a hassle.

### Things to Learn {#things_to_learn}

Here are things you\'ll probably want to learn as you get into
development.

-   Learn to search the source code \*efficiently\*\
    (if you see a word in the interface, chances are searching for it
    will take you to the code related to it, or close enough).
-   Learn basic regex (many search tools & IDE\'s support them), it
    looks confusing but you can find some cheat sheets online to help.
-   Learn how to create and apply patches (to share your changes with
    others)
-   Learn to use a debugger to at minimum check the file and line number
    of a crash (view a stack trace).
-   Use git to make temporary branches, stashing changes and apply
    patches.\
    *Allow for some time to become comfortable using git, initially
    simply updating is enough to get started, but eventually you will
    want to use some of it\'s more advanced features.*
-   Use \'`git blame`\' to find who changed some line of code, when and
    why.
-   Rather than guessing why something is slow, use a profiler - there
    are many profilers around and from my experience they all have
    tradeoffs between setup time, execution speed and useful output.
-   If you\'re debugging memory related errors on Linux.\
    Try
    [Valgrind](http://wiki.blender.org/index.php/Dev:Doc/Tools/Debugging/Valgrind)
    or
    [Address-Sanitizer](http://wiki.blender.org/index.php/Dev:Doc/Tools/Debugging/GCC_Address_Sanitizer),
    they\'re very useful tools and in some cases can save hours of
    tedious troubleshooting.

### Other Info {#other_info}

-   Often new devs ask what languages Blender is written in, you can
    check OpenHub for source code statistics/info\
    <https://www.openhub.net/p/blender/analyses/latest/languages_summary>
-   If you want to follow blender mailing lists without subscribing you
    can use markmail which has a useful search too\
    <http://blender.markmail.org>
-   For an overview of what to expect getting involved with development
    (that skips practical matters),\
    See:
    <http://blenderartists.org/forum/showthread.php?200013-develop-and-improve-the-source-code-of-blender&p=1721623&viewfull=1#post1721623>

```{=html}
<!-- -->
```
-   Official page for new-developers - see:\
    <http://wiki.blender.org/wiki/Developer_Intro/Overview>
-   For a general developer FAQ - see\
    <http://wiki.blender.org/wiki/Reference/FAQ>
