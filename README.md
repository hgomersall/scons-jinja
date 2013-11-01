SCons-Jinja
===========

A tool for SCons to enable building of [Jinja2](http://jinja.pocoo.org/) 
templates. It was originally created for C metaprogramming, in which the 
source C files were autogenerated from templates. The dependency tracking
is handled properly and is inferred from the template itself (that is, if
a C file depends on a template, which in turn depends on a second template,
then changes to the second template will trigger the necessary rebuilds).

For more infomation on Jinja itself, take a look at the 
[Jinja documentation](http://jinja.pocoo.org/docs/).

Nothing is C specific, so it should work fine with templates for anything.

A few examples are included (see below for how to use them).

Loading the tool
----------------

To enble the tool, copy `__init__.py` in this directory to a directory `jinja` 
in your tool path. In the case of a specific project, the tool path is 
something like `site_scons/site_tools/` 
at the same level as the project `SConstruct` file.

You can clone the repository directly into your `site_tools` directory with

    git clone https://github.com/hgomersall/scons-jinja jinja

(which creates a new directory `jinja` containing the repository wherever
you happen to be when the command is run).

Next, add the tool to an environment with something like the following:

    env = Environment(tools=['default', 'jinja'])

The `default` keeps all the default tools available.

Usage
-----

Once the tool is loaded, it can be used with the `Jinja` builder. e.g.

    env.Jinja('target_file_name.c', os.path.join('templates, a_template.c.jinja'))

The resultant target is then in dependency tree, so the following would work

    env.SharedLibrary('my_lib', env.Glob('*.c'))

Jinja templates are assumed to have the `.jinja` extension.

For more complete usage, see the examples in the examples directory.

Environment Variables
---------------------

Three new environment variables are available:

`JINJA_CONTEXT`: This is the context dictionary that is passed to the 
template when it is rendered.

`JINJA_ENVIRONMENT_VARS`: When the jinja environment is created during
the render phase, it is passed this dictionary as a set of keyword arguments.
The entries in this dictionary correspond to the initialization parameters 
described in 
[`jinja2.Environment`](http://jinja.pocoo.org/docs/api/#jinja2.Environment).

`JINJA_TEMPLATE_SEARCHPATH`: This provides additional paths that Jinja 
can use to look up dependencies. Templates that are named explicitly as a 
SCons node are provided with a path within the build tree. This path is the
default search path for dependencies. For example, if `env.Jinja` is called
for some template `templates/tmpl_1.c.jinja`, which is turn depends on
`tmpl_2.c.jinja`, which is also located in the `templates` directory, 
then `tmpl_2.c.jinja` will be found with no additional paths in 
`JINJA_TEMPLATE_SEARCHPATH`. If on the other hand, `tmpl_2.c.jinja` is in
some other directory, say `additional_templates`, then `additional_templates`
should be added to the `JINJA_TEMPLATE_SEARCHPATH` list. Note that these
paths are absolute paths or are relative to the SCons root (not, say, the 
SConscript path).

There can only be one set of
variables per environment, this means there is a one-to-one mapping between
SCons environments and Jinja environments. If you need render two templates 
with, say, different contexts, then you do it by
creating a new SCons environment for each template, each with their own
`JINJA_CONTEXT`.

Examples
--------

An example of various usages is included in the `examples` directory. 
To use this, you need to create an additional directory 
`examples/site_scons/site_tools/jinja` and copy `__init__.py` into 
that directory.

Run `scons` inside the examples directory. It should generate various 
outputs in various different ways.

`SConstruct` in `examples` and `SConscript` in `examples/src` show
the usage inconjuction with the various reference template files. The
`SConstruct` and `SConscript` files should be fairly self explanatory.

