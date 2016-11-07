Error and Warnings
===========================================

Compiler errors and warnings as well as Resharper errors and warnings are indications that there may be bugs or unexpected behavior in the code. Leaving errors and warnings, even when the solution builds "correctly" may serve to hide bugs from the developer. They will lessen trust in the solution and make it more difficult to ramp up other developers as they may present false indications of problems with the solution and need to be verified before being able to work on the project.

To foster confidence in the solution and maintain the effectiveness of the code analysis tools, every developer SHOULD eliminate all errors and warnings from their solution. Solutions SHOULD compile without errors prior to checking into source control.

Compile Errors
-------------------------------------------
All projects in a solution MUST build without errors. If a project is not used and has compile errors, those should still be corrected or the project should be removed from the solution. If the project is removed from the solution, but not removed from source control, the removal SHOULD be noted in the project README.

Compile Warnings
-------------------------------------------
All projects in a solution SHOULD build without warnings. Warnings that can be eliminated by producing more correct code, should be fixed in that way. Where the code itself is correct and the warning can safely be ignored, the warning should be suppressed with an appropriate comment or pragma suppression.  For example, if you enable XML comments documentation on a solution but choose only to document the public API, you may add the following to the file to eliminate warnings about missing XML comments.

    #pragma warning disable 1591

Warnings about mismatched reference versions SHOULD not be resolved using documentation, but should be corrected by
unifying the references used through-out the solution.

Resharper Error and Warnings
--------------------------------------------
Developers SHOULD eliminate Resharper errors that result in a "red halt" Resharper indicator.  The solution SHOULD have a "green checkmark" indicator before committing to source control.  Resharper errors may be suppressed using either comments or attributes where appropriate. Comments SHOULD indicate why the error has been suppressed.

Resharper warnings MAY be suppressed with comments or attributes where such suppression aids in readability. Resharper warnings are NOT REQUIRED to be suppressed.

**Resharper standards SHOULD be maintained even when some developers are not using Resharper.**