# Olivers-R-Scripts
Post-processing and stats for chromatin structures

**Using The Scripts**

At the moment these scripts have not been merged into a formal package.
As I create generalized versions of the scripts I will upload them here.
Please let me know if you find any bugs! (o.j.rimington@gmail.com)

To use them, download the relevant scripts to you your working directory in R.
Then: source("ScriptName.R")

Specific Functions are described below.

## Structural Max

Script: StructureMax.R

R Pre-Reqs: Hmisc, nnet

#####`StructureMax(MeanSample, PeakL, PeakR, ..)`

Iteratively adjusts the alignment of each chromatin region to obtain the most-structured representation of cumulative nucleosome positions. Each region is first spline-interpolated to a much higher resolution, a range of potential shifts are then tested on that region.

* MeanSample	-	This is a matrix of levels for feature-oriented regions of chromatin 

* PeakL			-	Integer. This is the user's chosen index for the -1 Peak (Use `FindPeaks` if uncertain)

* PeakR			-	Integer. This is the user's chosen index for the +1 Peak (Use `FindPeaks` if uncertain)

* prealign		-	TRUE/FALSE, Whether or not to pre-align on a single peak before. Only use without if chromatin is already well ordered. **Def=TRUE** 

* binSize		-	Integer, The number of base pairs in the binning method. **Def=10**

* stepSize		- 	Integer. Effects quantity and resolution of the alignment shift attempts, lower=better/more expensive. **Def=5**

* splineSize	-	Integer. Resolution of the spline interpolation. higher=better/more memory. **Def=10000**


Use:

```
myShifts = StuctureMax(meansMatrix, 144, 156, splineSize=5000)
```

Output:

'myShifts' is an object containing the two vectors of alignment shifts, and two matrices of interpolated and shifted regions. One is shifted to left hand maximum, the other shifted to the right hand maximum. This object also contains some contextual information relating to the alignment process which can be used in downstream functions.

The 'myShifts' object is then passed to the following functions:



#####'PlotStructureMax(MeanSample, Shifts, ..)`

* MeanSample	-	The original matrix supplied to `StructureMax`

* Shifts		-	The object returned by the `StructureMax` function

* save			-	TRUE/FALSE, Whether or not to save to file. **Def=FALSE**

* prefix		-	String. Prefix for the saved pdf filename. Format "prefixLRshifts.pdf". **Def=""**


Use:

```
PlotStructureMax(meansMatrix, myShifts, TRUE, "test1_")
```

Output:

A graph of the structural maximum alignments, including the pre-aligned trace. This also shows the relationship between the structural maximum and the central point of the region of interest. Usually this is the 'Transcription Start Site'.



#####'ApplyStructMax(Shifts, Sample, ..)`

Applies the alignments discovered in `StructureMax` to another sample.

* Shifts		-	The output object from `StructureMax`

* Sample		-	The matrix of feature-oriented chromatin structure regions for a single sample

* Right			-	TRUE/FALSE, flag determines whether the alignement takes place on the left or right hand structural maximum. **Def=FALSE**

Use:

```
Samp1Left = ApplyStructMax(myShifts, Samp1, FALSE)
``` 

Output:

The left or right struct. max aligned version of a sample.



#####'ApplyDualStructMax(Shifts, Sample)`

Applies both left and right hand structural maximum alignments, and merges the two region-sets at the mid-point. Useful for doing bootstrapping stats tests on the full aligned structures.

* Shifts		-	The output object from `StructureMax`

* Sample		-	The matrix of feature-oriented chromatin structure regions for a single sample

Use:

```
Samp1Both = ApplyDualStructMax(myShifts, Samp1)
```

Output:

A matrix, equal in dimensions to 'Sample', containing the left half of the left-hand structural maximum, and the right half of the right-hand structural maximum



#####'SaveShifts(Shifts, prefix)`

Utility function to save the alignment shifts object to file, to be re-loaded later. Shifts saved and re-loaded in the way CANNOT be used with `PlotStructureMax`

* Shifts		-	The object output from `StructureMax`

* prefix		-	A prefix to the saved file, can include directory address too. File name format is 'prefix.shifts'

Use:

```
SaveShifts(myShifts, "testSamp")
```

Output:

A file containing alignment shift data that can be loaded later.



#####'LoadShifts(filename)`

Utility function to re-load the alignment shifts from file. Shifts saved and re-loaded in the way CANNOT be used with `PlotStructureMax`

* filename		-	Path to, and name of, a file saved using `SaveShifts`. This will end in '.shifts'.

Use:

```
myShifts = LoadShifts(myShifts, "Some_Folder/testSamp.shifts")
```

Output:

A file containing alignment shift data that can be loaded later.



#####'FindPeaks(meanSample)`

Utility function to assist in the identification of -1 and +1 peaks for structural maximum alignment. Searches for peaks around the centre of the region.

* meanSample	-	The matrix to be used for `StructureMax`

Use:

```
FindPeaks(myShifts)
```

Output:

A short vector containing the indices of peaks found near the centre of the region. May be helpful in identifying -1 and +1 peak indices for `StructureMax`


##*Lots more to come..*