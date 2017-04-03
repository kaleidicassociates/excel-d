# excel-d

Excel API bindings and wrapper API for D

This dub package contains D declarations for the [Excel SDK](https://msdn.microsoft.com/en-us/library/office/bb687883.aspx)
 as well as a D wrapper API. This allows programmers to write Excel functions in D.

A working XLL example can be found in the [`example`](example)
directory. Running `dub build` there will create an XLL
(`myxll32.xll`) that can be loaded in Excel making all of the
functions in `test/xlld/test_d_funcs.d` available to be used in Excel
cells. The types are automatically converted between D native types
and Excel ones.  To build the example: `dub build -c example`.

For this package to build you will need the Excel SDK `xlcall32.lib`
that can be downloaded [from Microsoft](http://go.microsoft.com/fwlink/?LinkID=251082&clcid=0x409).
Copying it to the build directory should be sufficient
(i.e. when building the example, to the `example` directory).

The library file should be useable as-is, as long as on 32-bit Excel `dub build` is run with
`--arch=x86_mscoff` to use Microsoft's binary format. If linking with optlink, the file must
be converted first.  We recommend using `link.exe` to not need the conversion.  On 64 bit Excel
just use `--arch=x86_64` - no questions of different library formats.

Excel won't load the XLL automatically: this must be done manually in File->Tools->Add-Ins.
Click on "Go" for "Excel Add-Ins" (the default) and select your XLL there after clicking on
"Browse".

Currently only well-tested with 32-bit Excel 2013 on 64-bit Windows.  However there is support
for 64-bit - it's just not as well-tested.  The only differences are: for 32 bit you will need
the 32 bit version of xlcall32.lib, and for 64 bit the 64 bit version; and that the .DEF file
generated by traits.d is in a slightly different format for the 64 bit version.  The version
to generate is determined at compile-time by a version switch.

Sample code:


	module xlld.test_d_funcs;

	import xlld.worksheet;

	@Register(ArgumentText("Array to add"),
	          HelpTopic("Adds all cells in an array"),
	          FunctionHelp("Adds all cells in an array"),
	          ArgumentHelp(["The array to add"]))
	double FuncAddEverything(double[][] args) nothrow @nogc {
	    import std.algorithm: fold;
	    import std.math: isNaN;

	    double ret = 0;
	    foreach(row; args)
	        ret += row.fold!((a, b) => b.isNaN ? 0.0 : a + b)(0.0);
	    return ret;
	}


and then in Excel:

`=FuncAddEverything(A1:D20)`

Future functionality will include creating menu items and dialogue boxes.  Pull requests welcomed.