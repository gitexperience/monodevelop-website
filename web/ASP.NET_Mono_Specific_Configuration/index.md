---
Title: ./ASP.NET_Mono_Specific_Configuration
layout: default
---

by [Marek Habersack]({{site.url}}/User:MarekHabersack "wikilink")

**Note: this document applies to the SVN trunk version of Mono (as of 10
October 2008) and to Mono 2.1 and newer**

In certain situations the Mono implementation of ASP.NET must adapt to
different environment conditions than those commonly found when the
application runs under Microsoft .NET. To make it easier for the user to
configure Mono for its controlling operating system we have added a
sub-section to the `&lt;system.web&gt;` section of the `Web.config` file
- **\<monoSettings\>**.

This article contains detailed information on the section contents.

Section attributes
------------------

The following table describes all the attributes currently defined for
the section tag. Number in brackets preceeding each key is Mono version
in which the key first appeared:

**[2.1] useCompilersCompatibility** : a boolean which tells ASP.NET whether or not to augment compiler information read from either `&lt;system.codeDom/compilers&gt;` or `&lt;system.web/compilation&gt;` configuration sections. Defaults to **true**. For more details about the compiler settings available see [below]({{site.url}}/compilersCompatibility "wikilink")

**[2.4.2] verificationCompatibility** : an integer which serves the same purpose as the **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\ASP.NET\\VerificationCompatibility** Win32 registry key (described in [this article](http://support.microsoft.com/kb/932552)) - to let the user turn off virtual path validity verification. When Mono runs under Windows it will first check the value of the above registry key and then consult web.config to check for value of the property described here. See also [this bug report](https://bugzilla.novell.com/show_bug.cgi?id=509163).

Child elements
--------------

### compilersCompatibility

This is a standard `add/clear/remove` section which follows the same
syntax as the `&lt;system.web/compilation&gt;` section. The difference
is that Mono ignores all settings except for the `compilerOptions` one.
The corresponding `&lt;system.codeDom/compilers&gt;` or
`&lt;system.web/compilation&gt;` entry is augmented **only** if the
compiler type matches. The compiler options read from this section are
appended to the default ones.

#### Current compatibility settings

The current default configuration modifes the C\# compiler entry in the
following ways:

##### C\# compiler: /nowarn:0169

Appends the `/nowarn:0169` option to the compiler command line.

This prevents situations when code similar to the one shown below would
throw an exception if the user configured the C\# provider with the
**/warnlevel:3 /warnaserror** options. The .NET csc compiler does not
throw report this error in this particular situation:

    <%@ Page Language="C#" %>
    <script runat="server">
    void Page_Load(object o, EventArgs e) {
    }
    </script>

**Note:** If you plan to use `#pragma warning reset 169` anywhere in
your application code, your source will fail to compile - you need to
either disable the compiler compatibility settings or override the C\#
compiler entry removing the `/nowarn:0169` option from the
**compilerOptions** attribute.

**Note:** An easy way exists to prevent this error from happening. It's
sufficient to modify the code presented above by making the Page\_Load
method a `protected` one. No compiler warning will be reported then.

[Category:Developer Resource]({{site.url}}/Category:Developer Resource "wikilink")
<Category:ASP.NET> <Category:Porting>
