# 2016-01-08 subset of ~440k Cornell records

~440k records have been selected from the complete catalog that was converted first to BibFrame with the LC converter, and then to the LD4L ontology. The LD4L ontology data is split into several files:

```
simeon@RottenApple mx>ls -lh /cul/data/ld4l/2016-01-08_cornell/
total 1753672
-rw-r--r--  1 simeon  staff   1.1M Jan  7 16:57 bfAgent.nt.gz
-rw-r--r--  1 simeon  staff    12K Jan  7 16:57 bfEvent.nt.gz
-rw-r--r--  1 simeon  staff   1.8K Jan  7 16:57 bfFamily.nt.gz
-rw-r--r--  1 simeon  staff    21M Jan  7 16:58 bfHeldItem.nt.gz
-rw-r--r--  1 simeon  staff   458M Jan  7 17:06 bfInstance.nt.gz
-rw-r--r--  1 simeon  staff   195K Jan  7 17:06 bfLanguage.nt.gz
-rw-r--r--  1 simeon  staff   2.5M Jan  7 17:06 bfMeeting.nt.gz
-rw-r--r--  1 simeon  staff    20M Jan  7 17:07 bfOrganization.nt.gz
-rw-r--r--  1 simeon  staff    58M Jan  7 17:08 bfPerson.nt.gz
-rw-r--r--  1 simeon  staff   1.0M Jan  7 17:08 bfPlace.nt.gz
-rw-r--r--  1 simeon  staff   499K Jan  7 17:08 bfTemporal.nt.gz
-rw-r--r--  1 simeon  staff    39M Jan  7 17:09 bfTopic.nt.gz
-rw-r--r--  1 simeon  staff   248M Jan  7 17:15 bfWork.nt.gz
-rw-r--r--  1 simeon  staff   5.2M Jan  7 16:42 newAssertions.nt.gz
-rw-r--r--  1 simeon  staff   2.6M Jan  7 17:15 other.nt.gz
```

The file `newAssertions.nt.gz` contains as set of `owl:sameAs` assertion from the BF/LD4L Instances (which have URIs based on the local Cornell MARC `bibid`) and OCLC WorldCat ids derived from the OCLC numbers stored in out MARC data:

```
simeon@RottenApple 2016-01-08-subset>zmore newAssertions.nt.gz | head -10
<http://draft.ld4l.org/cornell/n46182instance164> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/3990662> .
<http://draft.ld4l.org/cornell/n260578instance172> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/5751223> .
<http://draft.ld4l.org/cornell/n75795instance198> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/10375672> .
<http://draft.ld4l.org/cornell/n146324instance68> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/1812216> .
<http://draft.ld4l.org/cornell/n91782instance166> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/5880991> .
<http://draft.ld4l.org/cornell/n263536instance126> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/2826968> .
<http://draft.ld4l.org/cornell/n168303instance95> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/1318622> .
<http://draft.ld4l.org/cornell/n119271instance50> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/1101221> .
<http://draft.ld4l.org/cornell/n76760instance162> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/10924009> .
<http://draft.ld4l.org/cornell/n16624instance20> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/63222502> .
```

In order to use some old code that works with `bibids` and OCLC numbers to find OCLC work ids, we can extract a table of `bibid oclcnum` pairs by pattern matching on the ntriples data:

```
simeon@RottenApple 2016-01-08-subset> zcat newAssertions.nt.gz | perl -ne 'if (m%^<http://draft.ld4l.org/cornell/n(\d+)instance.*oclc/(\d+)>.*%) { print "$1 $2\n"; }' | gzip -c > 2016-01-08_cornell_bibid_oclcnum.dat.gz
```

It seems there is one bad/odd line in the `newAssertions.nt.gz` data that we'll ignore for now:

```
simeon@RottenApple 2016-01-08-subset>zcat newAssertions.nt.gz | wc -l
  437369
simeon@RottenApple 2016-01-08-subset>zcat 2016-01-08_cornell_bibid_oclcnum.dat.gz | wc -l
  437368
simeon@RottenApple 2016-01-08-subset> zcat newAssertions.nt.gz | perl -ne 'if (not m%^<http://draft.ld4l.org/cornell/n(\d+)instance.*oclc/(\d+)>%) { print "$_\n"; }'
<http://draft.ld4l.org/cornell/n366803instance219> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/42467506.> .
```

Taking this table of `bibid oclcnum` and the OCLC concordance data relating OCLC numbers to OCLC works, and [code to look up the work ids](https://github.com/zimeon/mx/blob/master/mx_get_oclc_workids.py). We can run to get find work ids:

```
simeon@RottenApple 2016-01-08-subset>time ../mx/mx_get_oclc_workids.py --write-workid-bibids=work_bibs.dat.gz --write-oclcnum-workid-pairs=oclcnum_work_pairs.dat.gz --write-pairs=bib_work_pairs.dat.gz2016-01-08_cornell_bibid_oclcnum.dat.gz oclcnum_workid_concordance.txt.gz 

real    92m15.142s
user    28m19.810s
sys 0m4.324s
```

From this we see that there are 437251 oclcnum to work id pairs:

```
simeon@RottenApple 2016-01-08-subset>zcat oclcnum_work_pairs.dat.gz | wc -l
  437251
```

which means that we have matched to get a work id for all but 117 of the 437368 OCLC numbers we started with (99.97%).

Note that the data we have includes instances of the obvious case where we have multiple instances (`bibids` and OCLC numbers) of a single work (OCLC work id). However, there are also cases where a single `bibid` MARC record includes multiple OCLC numbers, see for example:

  * <https://newcatalog.library.cornell.edu/catalog/368365> "Wirtschaft und Statistik"
 
which has multiple versions and supplements with different OCLC numbers. These show up in the `newAssertions.nt.gz` data:

```
simeon@RottenApple 2016-01-08-subset>zgrep 'n368365instance' newAssertions.nt.gz 
<http://draft.ld4l.org/cornell/n368365instance168> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/1644252> .
<http://draft.ld4l.org/cornell/n368365instance176> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/33144347> .
<http://draft.ld4l.org/cornell/n368365instance177> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/25754870> .
<http://draft.ld4l.org/cornell/n368365instance178> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/69678251> .
<http://draft.ld4l.org/cornell/n368365instance179> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/64633575> .
<http://draft.ld4l.org/cornell/n368365instance180> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/52542836> .
<http://draft.ld4l.org/cornell/n368365instance181> <http://www.w3.org/2002/07/owl#sameAs> <http://www.worldcat.org/oclc/51313033> .
```

and they map to a single work even if represented as multiple matches in the `bib_work_pairs.dat.gz` file:

```
simeon@RottenApple 2016-01-08-subset>zgrep ' 368365' bib_work_pairs.dat.gz 
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
378647035 368365
```

The script [`mx_analyze_workids.py`](https://github.com/zimeon/mx/blob/master/mx_analyze_workids.py) is designed to look at the bidid-workid data and, among other things, writes histogram data to its log file:

```
simeon@RottenApple 2016-01-08-subset>../../mx/mx_analyze_workids.py -v bib_work_pairs.dat.gz out.gz
simeon@RottenApple 2016-01-08-subset>cat mx_analyze_workids.log | perl -ne 'if (m%WARNING:root:histogram: (\d+ \d+)%) { print "$1\n"; }' > histogram_of_num_works_with_given_num_bibids.dat 
simeon@RottenApple 2016-01-08-subset>more histogram_of_num_works_with_given_num_bibids.dat 
1 418894
2 5983
3 743
4 189
5 72
6 32
7 10
8 5
9 2
10 3
11 5
12 1
13 1
14 1
18 1
28 2
```

So then, <histogram_of_num_works_with_given_num_bibids.dat> is a histgram where the first column is the number of bibids associated with (instances of) a given work, and the second column is the number of works that meet this condition. Hence we see that there are ~419k works for which we have one instance in this dataset, ~6k works for which we have two instances, etc., all way down to there being 2 works for which we have 28 instances. One of these works is:

  * <http://worldcat.org/entity/work/id/682867933> "Projections of educational statistics to"

and the instances in Cornell's catalog include:

  * <http://newcatalog.library.cornell.edu/catalog/287484>
  * <http://newcatalog.library.cornell.edu/catalog/291240>
  * <http://newcatalog.library.cornell.edu/catalog/297499>
 
which are all outputs from the National Center for Education Statistics although it is not really clear that they should be grouped as one work.

Perhaps a clearer example is a popular physics textbook:

  * <http://worldcat.org/entity/work/id/3145862> "Quantum mechanics" by Leonard Schiff

where there are three instances in the subset data:

  * <http://newcatalog.library.cornell.edu/catalog/80478> 1st edition
  * <http://newcatalog.library.cornell.edu/catalog/60989> 2nd edition
  * <http://newcatalog.library.cornell.edu/catalog/60996> 3rd edition



