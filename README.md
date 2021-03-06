# DMD Linker Prob #

  A linker problem I encountered earlier and resolved with the help of Adam Ruppe ( @destructionator ).

## Steps To Reproduce ##

* Clone DChip from [here](https://github.com/d-gamedev-team/dchip)
* Add dchip to your dub repository
`dub add-local <path_to_dchip>`
* Run dub in this repo to verify linker problem

`dub`

or

`dub --config=doomedToFailure`

* Verify the workaround (should succeed this time)

`dub --config=workaround`

## What's going on? ##

Good question :-) It looks like copying a pointer to a cpBody struct in the line.

`bods ~= bod;`

is causing dmd to insert a reference to a function which fails to link.

`Linking...
.dub/build/doomedToFailure-debug-linux.posix-x86_64-dmd_2068-526FACB894EDABA2E3C35CF8E3068673/dmdlinkerprob.o:(.data._D30TypeInfo_S5dchip6cpBody6cpBody6__initZ+0x38): undefined reference to _D5dchip6cpBody6cpBody11__xopEqualsFKxS5dchip6cpBody6cpBodyKxS5dchip6cpBody6cpBodyZb
collect2: error: ld returned 1 exit status`


Running `ddemangle` on the linker error shows that the bad function is.

`bool dchip.cpBody.cpBody.__xopEquals(ref const(dchip.cpBody.cpBody),
ref const(dchip.cpBody.cpBody))`

Adam's workaround is to force dmd to pull in the definition of cpBody which (presumably) pulls in the definition of the missing function...

`auto x = typeid(cpBody).toString();`

Running dub with `--config=workaround` enables a version block which includes Adams workaround. So the code compiles.

### Emacs compilation commands ###

   Compilation commands that can be executed with 'C-x C-e' after the s-expr

   `(compile "cd ~/repos/dmdLinkerProb && dub --force --config=doomedToFailure")`
   
   `(compile "cd ~/repos/dmdLinkerProb && dub --force --config=workaround")`



