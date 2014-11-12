#Copy Phase\
***

###概述

这个是获取map结果输出的过程，然后交给reduce去处理。

主要是包括以下几个方面
* GetMapEventThread
* MapOutputCopier
* InMemFSmergeThread
* LocalFSMergeThread
* fetchOutputs

###几个方面之间的关系

![CopyPhase](/_image/4.1.CopyPhase.png)

###map结果的分类记录

map结果分类记录和各个线程之间的关系还会是很复杂的。

![CopyPhaseRecords](/_image/4.2.CopyPhaseRecords.png)

