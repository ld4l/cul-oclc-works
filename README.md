cul-oclc-works
==============

Data relating to Cornell Univeristy Library catalog records to OCLC work ids. See also Stanford University Library data at <https://github.com/ld4l/sul-oclc-works>. This work relies upon a concordance of control numbers and work ids provided by OCLC. Data available:

  * `cul_workid_bibids.dat.gz` - Each line (except comment lines starting with #) is a space separated sequence of oclc_work_number, followed by one of more cornell_bibid numbers
  * `cul_oclcnum_workid_pairs.csv.gz` - Each line is a comma separated pair of values: oclc_control_number,oclc_work_number (created to follow format of Stanford file in <https://github.com/ld4l/sul-oclc-works/blob/master/SUL_OCLCcn2OCLCwn.tar.bz2>)

The data comprises just integer values:

  * To get a resolvable URI from a cornell_bibid, prefix with `http://newcatalog.library.cornell.edu/catalog/`, e.g. <http://newcatalog.library.cornell.edu/catalog/1711039>.
  * To get a resolvable URI from an oclc_control_number, prefix with `http://www.worldcat.org/oclc/`, e.g. <http://www.worldcat.org/oclc/8712037>.
  * To get a resolvable URI from an oclc_work_number, prefix with `http://worldcat.org/entity/work/id/`, e.g. <http://worldcat.org/entity/work/id/515675>.

This repository is for internal (LD4L)<http://ld4l.org/> work, it may not be generally useful outside the LD4L context.
