# cellcount

<h2>Summary</h2>

This write-up walks through image techniques that turn a photograph into a number: how many objects are inside it. The pipeline detects the wanted shape and tallies instances by working on edge maps, then estimates object density so that integrating over any region of the picture yields a count for that region.

<h2>Why it matters</h2>

Tallying objects by hand is slow, monotonous and non-automatic, and it shows up across many industries. Any analysis that needs to know how many items sit in a frame depends on it — quantitative studies, motion tracking, qualitative review. Long manual sessions cause eye strain and the numbers drift. Automated tallying is repeatable and objective, so comparing counts between specimens is far more trustworthy than with a human doing the counting; the automatic method returns a stable value each time while a manual count varies from run to run. The system should detect and count every object in the frame on its own.

<h2>Pipeline</h2>

<h3>Gradient edge extraction</h3>

After high-pass filtering, an operator pulls out boundaries around the cells. Several sub-steps run here: double thresholding, differentiation based on intensity change, and dropping edges that are not real. Secondary edges are recovered through connected components, and the removal stage is also handled at this point.

![image](https://user-images.githubusercontent.com/53599318/100911930-dfb8e380-34f5-11eb-97c7-5be98abdc3b2.png)

<h3>Closing the gaps</h3>

Closing means dilating first and eroding afterwards. It plugs small holes inside foreground blobs and suppresses stray points, which makes it useful for bridging the short breaks that appear along cell boundaries.

<h3>Finding contours</h3>

Contours are the closed regions produced by the edge stage. Locating them indirectly gives the set of cells in the frame. The whole approach for red blood cell counting rests on three ideas: logic, morphology and contour detection.

<h2>System model</h2>

![architecture](https://user-images.githubusercontent.com/53599318/100908918-142aa080-34f2-11eb-8b73-6be0034b0ed9.png)
  
  Methods compared:

1. The density-based proposal.

2. The regression baseline.

3. The detection baseline.

4. A task-specific approach.

<h2>Outcome</h2>

![image](https://user-images.githubusercontent.com/53599318/100912343-5524b400-34f6-11eb-83dd-d60e458651f2.png)

![image](https://user-images.githubusercontent.com/53599318/100912698-d2e8bf80-34f6-11eb-917d-8a1966f67072.png),![image](https://user-images.githubusercontent.com/53599318/100910633-37eee600-34f4-11eb-9a7e-bf9871ed60dc.png)

![image](https://user-images.githubusercontent.com/53599318/100910729-505f0080-34f4-11eb-9bef-9ddbd1ad31c6.png),![image](https://user-images.githubusercontent.com/53599318/100910772-5e148600-34f4-11eb-8f29-891c2dda4187.png)

<h2>Takeaways</h2>

• Quality and quantity are scored separately. The new plots make both readable at a glance — how many instances were found, how many were false alarms, and how accurate each hit was.

• The dataset itself gets measured too: performance is mapped against the makeup of the test collection, so the advantage one detector holds on collections with more relevant content becomes obvious.

• A single threshold-free score is derived. It cannot describe a detector completely, but it makes ranking candidates straightforward.

<h2>Closing</h2>

The end goal is an accurate object count for any given frame. The program therefore detects the target shape on its own and counts instances by working from the edge map, then estimates density so that integrating over any region returns the number of objects in it.