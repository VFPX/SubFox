# SubFox Design Documentation
**Seamless integration for Subversion source code control**

This documentation is for FoxPro developers participating in the development of SubFox itself, or enhancing SubFox to address their unique needs.

## Overview
Subversion is capable of handling binary files, but it can only merge changes in text files. Visual FoxPro uses the native DBF format to save most of it's source code (all but PRG and H files). In order to resolve this problem, SubFox incorporates a custom translation library for encoding and decoding these files.

SubFox is not the first to conceive this approach. VFP comes pre-packaged with just such a translator called SCCTEXT.PRG. Before undertaking to write our own translator, we considered of using SCCTEXT but eventually decided against it because the text format of files encoded by SCCTEXT were so seriously challenging to read -- as text -- that performing conflict resolution using these files was always very traumatic. Another excellent translator developed by Christof Wollenhaupt was also considered, but it too suffered from excessively complex output files slavishly devoted to the XML format. We have tried to incorporate the best elements of these fore-runners into the SubFox translator.

The Subversion repository must only be responsible for tracking source files. In the case of conventional PRGs, the compiled output files have an FXP extension. FXP files should never be introduced into a Subversion repository because they are a byproduct of a compile, and any changes therein are merely the consequence of meaningless details such as the _time_ the file was compile, or from far more meaningful changes in the original PRG file which is captued separately from the FXP.

This same principle applies to all binary VFP files. The _source file_ is the encoded textual version which, when decoded, produces a usable VFP resource as a _byproduct_. You should never introduce a native VFP binary file (such as a SCX or SCT file) into a Subversion repository. SubFox conveniently names it's encoded files by adding the extension ".subfox" to your base file. So for example, MyForm.scx would be encoded into myform.scx.subfox.