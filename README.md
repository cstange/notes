# notes

chef, puppet
http://news.ycombinator.com/item?id=3090800
android internals
apps: bundles of components
each app in a linux process; can be killed any time
intents: msgs, like unix signals
Binder: RPC system that lets services talk to components & to ea other
IDL
bionic: libc replacement
dalvik vm, harmony class lib
dx converts .class to .dex
init: parses /init.rc; usu mounts FSs, starts mem mgr, starts services (incl servicemanager, which manages Binder ctxs), starts zygote
zygote aka app_process: root java process; starts system service
system service: process that houses everything important (battery, lights, vibrator, audio, sensors, etc), alarms, notifs, activities, windows
toolbox: suckier busybox; freq install busybox on androids
startup
bootloader implements fastboot protocol; contains secure boot/recovery logic
no FHS but no conflicts either
ext4 for SMP and new storage devices that were more like SD cards than NAND flash; was yaffs2
android development
paths
Environment.getExternalStorageDirectory: /mnt/sdcard
shared space, very user-accessible, public
generally avoid using this
Environment.getExternalStoragePublicDirectory(“AnyStringExceptNull”): /mnt/sdcard/AnyStringExceptNull
shared space, very user-accessible, public
may not exist; create with File.mkdirs()
Context.getExternalFilesDir(“blah”): /mnt/sdcard/Android/data/com.android.test/files/blah
app-specific, public
auto-creates directory
null gives the base dir, not a subdir
Context.getFilesDir: /data/data/com.android.test/files
app-specific, private
mahout
? means unknown
mahout accepts csv-like files where ‘,’ and ‘’ are delims (it also ignores the @ lines so this way it also accepts ARFF)
linux security tools/practices
denyhosts/fail2ban
psad: detect intrusions from iptables
tiger: general system audit tool
logwatch
chkrootkit
ufw
sshd_config: PermitRootLogin no and PasswordAuthentication no
http://www.andrewault.net/2010/05/17/securing-an-ubuntu-server/
machine learning libraries
orange > pyml, mlpy
rapidminer > weka
weka
trees
REPTree: does reduced-error pruning; considers all attrs at each node
RandomTree: no pruning; considers only random subset of attrs at each node
J48: C4.5 impl
http://mydatamining.wordpress.com/2008/04/14/decision-trees/
dealing with dataset imbalance
SpreadSubsample: undersampling
SMOTE: oversampling
CostSensitiveClassifier
InterquartileRange: undersample (remove) outliers
CostMatrix, Resample
http://old.nabble.com/Unbalanced-class-problem-td29911621.html
google apps script
run on google servers
can customize buttons/menus
per-document, or can be added domain-wide
can trigger on events: open, edit, install, spreadsheet form submit, etc
can publish into public gallery or as a private or public URL service (where it still runs as you)
various actions: send mail, fetch URLs, manage docs, jdbc, utils
speech synthesis/TTS systems
festival: c++/scheme; basic (diphone) voices poor; HTS/Unit Selection voices great
flite: c
emacspeak: dectalk 3, dectalk express, viavoice
espeak: c
freetts: java
festvox: voices
mary: sounds pretty nice, at least the online demo w cmu voices
epos
hts (hmm text to speech)
mbrola: not that great
neospeech: commercial; amazing; used in qwiki
svox: used in google products
process monitoring/management
supervisord
directly monitors children; no sub-children, no forking/start cmds
ugly configparser files
less control than god
only very basic monitoring controls + event-listening extension system
online reloading (reread then update) isn’t that great
misses certain changes, e.g. to events for event listener
changed processes are restarted on update
only one group per program
god
reports of problems running and memory leaks
http://stackoverflow.com/questions/768184/god-vs-monit
http://blog.bradgessler.com/use-monit-with-rails-not-god
I also had problems with god hanging when playing with it
unmaintained since Feb 2009
monit
nice but new language
most actively maintained
unlike god/supervisord:
only supports running in fresh environment
no stdout/stderr redirection
only uses forking/start cmds; no child supervision
advanced monitoring checks
dependencies, but only process-based, not listening port-based
janky; single-threaded architecture gets stuck waiting for pidfile to appear and can’t respond to control
very slow/sluggish feeling
can monitor process trees, unlike the others
upstart
designed for child supervision
root user only; only supports running as root
simple, restricted language
no stdout/stderr redirection
also actively maintained; ubuntu’s “success story”
dependencies
details
“stop on stopped foo” will stop this job even if foo just dies (doesn’t have to be explicitly stopped)
stopping: first sends TERM, then KILL after 5s (kill timeout 5)
log if exited with non-0, logged
upstart takes care of start on runlevel [2345]
don’t create /etc/rc?.d/ symlinks
ubuntu has symlinks in /etc/init.d/ to upstart-job only for compat
perhaps users or other programs expect these there
none of them have symlinks in /etc/rc?.d/
nagios
pros/features/highlights
event processing system; events can be restarts, alerts, logging, etc.
large ecosystem; biggest of all monitoring systems; many existing plugins
web interface to see current status
passive as well as active plugins; not just cron
simple plugin API: any command line tool
results caching, service dependencies, adaptive monitoring
on-call notification rotations, escalations
cons
very easy to re-implement these checks
yet another system to learn, with its own config labyrinth (self-advertised as complex)
everything is periodic, has delay
no integration with trend monitoring systems; no data accumulation
how is it for EC2-like places where instances are expected to fail?
polling duration can exceed period, causing problems
details
define hosts, cmds, services (what cmds to run on what hosts)
external command file: where other programs send nagios internal cmds (not cmd-line cmds) to run
active plugins: exit status 0-3 determine state
distributed montoring: ssh/NRPE for active, NSCA for passive, SNMP
passive plugins: checks external command file every minute by default
can check for sufficient freshness
both queue results; checked every 5s by default by reaper
volatile: state-triggered (v edge-triggered)
ndoutils: store in mysql; will probably be nagios default in future
stalking: logging all
often integrated with cacti/rrdtool for trend monitoring?
monitoring systems (2008)
nagios: see section
zenoss (core): zope
web/daemon/data-tier arch; data in cmdb (cfg), rrd (hist), mysql (events)
uses snmp (req’d), nagios plugins, zenpacks (cmds, tmpls, graphs)
all cfg via web ui or api
tmpls; production states for servers; alert severities; locations
integrates events and trends; nice/confusing gui
scalable server
missing commercial features in zenoss enterprise
zabbix
lightweight agent/php/db-tier arch
simple checks; agents; snmp; external/internal/aggregated checks
auto discovery (agent based)
fully integrated alerting, reporting, trending
create own screens w tmpls; correlation of graphs
cons: cumbersome config; reporting not great; no escalation
hyperichq
java; pg
focus on app internals (mysql, pg, jboss); great for debugging
integrated with opennms; similar frameworks; complementary
auto-detection; best graphing; easy config; nice GUI
not for typical lamp shop; lots of features in commercial version
opsview enterprise
set of nagios exts for scaling, web (catalyst), mysql
HA/LB monitoring; multi slaves, single master
nagvis, rrdtool for trending
opennms: once nagios’ only contender; j2ee; focus on network; snmp + nagios plugins
groundworks: sucks
hobbit: sucks
from http://www.slideshare.net/tomdc/open-source-monitoring-tools-shootout
nice feature matrix
scipy
scikits
openopt: bunch of optimization
sqlite
be careful, int != integer:
sqlite> create table a (a integer primary key, b integer);
sqlite> insert into a (b) values (0);
sqlite> select * from a;
1|0
sqlite> create table b (a int primary key, b integer);
sqlite> insert into b (b) values (0);
sqlite> select * from b;
|0
R
packages
misc
Rserve
foreach
parallel
lubridate
data
RPostgreSQL
RGoogleDocs
sqldf: SQL on DFs
plyr: general bulk manipulation utils
reshape2: pivot table-like cubing/rollups
data.table: a better data.frame
xtc: builds on zoo; “unifies” the various time series pkgs
model selection
leaps: stepwise/exhaustive, AIC/BIC/R2/…
lars: LARS, LASSO, fwd stagewise
OLS only
faster than glmnet for small/sparse/wide problems: http://stats.stackexchange.com/questions/7057/glmnet-or-lars-for-computing-lasso-solutions
lasso2: L1 for GLM
clustering
mclust: mixture modeling, model-based clustering
modeling
randomForest
survival
rjags
mgcv: smoothers for GLMs
caret: data splitting, preprocessing, model tuning using resampling, variable importance estimation
train: parallel training procedure that takes care of a lot of stuff for you
knn
ROCR
prediction
performance
kernlab
e1071: misc functions from dept stats, TU Wien
NB, SVM, perms/combs, shortest paths, distances, LCA, lots more
tm: term document matrices
irlba: SVD for big data
arm: bayesglm
forecast: AR, ARIMA, exponential smoothing, etc. (hyndman)
assorted
MASS: companion to modern applied statistics with s
rlm: robust regression
boxcox
stepAIC: fuller version of step
numeric
quadprog
eval
verification: fast roc.area & other measures
qcc: quality control charts, ROCs, etc.
data
discretization: mdlp same as orange.EntropyDiscretization
viz
ggplot2
ggExtra: install.packages("ggExtra", repos="http://R-Forge.R-project.org")
rggobi
directlabels
lattice (splom), car (spm): scatterplot matrices (see ggplot2)
maps, geosphere
popbio
logi.hist.plot: viz logistic regression results
hexbin
HH
ci.plot: confidence interval plot http://princeofslides.blogspot.com/2011/02/sab-r-metrics-basic-applied-regression.html
finance
quantmod
quantstrat
BurStFin
vocab
modeling facilities
expressions: y ~ a + b + c:d + e*f - b (c:d means interaction, e*f means e + f + e:f)
linear models: lm, glm
clustering: kmeans
optimization: optim, nlm
ANOVA: aov, anova
descriptive tools: table, summary
gbm: gradient boosting machine
nonlinear models: loess, scatter.smooth
model selection: step
eval: rstandard, rstudent, AIC, BIC
misc: predict, resid, coef, confint, cooks.distance, diffits, influence.measures
stats
cor, cov, var, mean, median
summary, table
system
install.packages, update.packages
setwd, read.csv
options, sessionInfo
trace, traceback, stop, stopifnot
png, dev.off, par, dev.new
manipulation
transform, head, tail, subset, cbind, rbind, colnames
abline, lines
str, dput
all.equal, identical
new.env, with, within, search, ls, attach, detach, get, assign, <<-, <-
sort
na.*
match, %in% (subset), is.element, setdiff, merge
paste
apply, lapply, sapply, tapply
rbind.fill
cut
control
invisible
syntax tricks
use backticks for non-standard names
to run an R file from cmd line
R --vanilla "--args ${@:2}" < $1 #>/dev/null
will be replaced with Rscript
GUIs: RStudio beats all the rest
pitfalls
don’t pass in logicals as y to train; use factor(ifelse(x, 'pos', 'neg'))
RF can only have categorical variables with <=32 factors http://r.789695.n4.nabble.com/randomForest-can-not-handle-categorical-predictors-with-more-than-32-categories-td3036871.html
gripes
syntax: can never tell when something will be evaluated, when i’m writing symbols or formulae or expressions, etc.
inconsistency: packages use model formulae, vectors/matrices, and/or data.frames
hard to find packages, determine best packages, etc.
memory management/fragmentation
caret
can’t dynamically choose params (eg glmnet lambda, randomForest mtry)
smart cards
form factors: credit card, mini-SIM (most common), micro-SIM
communication w PCs
PC/SC (personal computer/smart card): serial protocol; in Win; PC/SC Lite for Linux
CCID, ICCD: USB protocols
ubuntu
-dbgsym packages have debug symbols for all binaries
user adm is for viewing system logs; user admin is for sudo
debian
apt
/var/run/reboot-required: presence means need to reboot
LDAP
hierarchical directory of entries of KV attr pairs (defined by schema)
schemas define object classes; published at a base DN in subschemaSubentry operational attr (system attr)
obj ID is distinguished name (DN)
topmost levels usu. reflect DNS (eg dc=example,dc=org)
directory system agent (DSA): an LDAP server; default port 389
can refer to other servers
request types
start TLS
bind: authenticate w user DN and passwd, specify version (normally LDAPv3)
search: base obj, scope, filter, deref aliases?, attrs (projection), size/time limits, types only?
compare: check DN has attr pair
add/delete/modify: takes DN and list of attrs
extended ops: eg cancel, passwd modify
abandon: abort an operation named by a msg ID; unfortunately no response, so cancel extension was introduced
unbind: abandon ops, close conn
active directory
LDAP, modified KRB, DNS, SSO, app roaming storage, admin/policy/deploy SW/etc
AD includes 1+ domains, each w 1+ domain controller (DC) (can scale)
AD app mode (ADAM): lightweight impl on 2k3/XP; same but no need for domain
primary DC, backup DC, or domain member: older NT model
now 1+ equal peer DCs; multi-master repl (configurable sync/async/etc)
introduced in win2k
samba: impls SMB/CIFS and domain membership; v4 can serve as AD DC (future)
NBT, SMB/CIFS, DCE/RPC, MSRPC, network neighborhood, WINS server called NBNS, SAM, LSA, SPOOLSS, NTLM, AD logon
smbd and nmbd
pylons/paster
paster
sys.exit(load_entry_point('PasteScript','console_scripts','paster'))
main code calls get_commands to find all paster plugins (uses entrypoints?)
--plugin for extensibility
pylons
appconfig loads config
loadapp loads config and creates app
loadserver loads config and creates server
all above take URLs like config:development.ini
paster setup-app development.ini
[app:main] in .ini points to egg for your app (“myapp”)
myapp’s paste.app_install entrypoint is pylons.util.PylonsInstaller
Installer objects run setup_app (or setup_config) in websetup
passes paste’s appconfig() into setup_app
paster serve development.ini
serve command calls loadapp and loadserver
loadapp uses entrypoint paste.app_factory, points to myapp.config.middleware
middleware is passed global_conf ([DEFAULT] in .ini) and app_conf ([app:main] in .ini)
calls load_environment: creates/returns pylons.config = pylons.configuration.PylonsConfig
creates PylonsApp as stack of middleware initialized by config
the config is then registered into pylons.config by PylonsApp via paste.registry.register (pushed onto a stacked proxy object)
config has local_conf and global_conf sub-dicts
http://pylonsbook.com/en/1.1/pylons-internal-architecture.html
http://wiki.pylonshq.com/display/pylonscookbook/Pylons+Execution+Analysis+0.10
python packages/eggs
distributed/pip: successor to setuptools/easy_install
pkg_resources: most of the meat is in this module
entry points: simple global registry for all modules to declare what interfaces they provide (and for others to find them by these interfaces)
egg metadata stored in ./package.egg_info/ or site-packages/package.egg/EGG-INFO
net booting, old to new: etherboot (95), intel PXE (99, TFTP), gPXE (SAN)
android
processes can be shared by multiple apps
on backgrounding, apps are signaled to save their state
OS will kill bg procs under pressure
philosophy: don’t distinguish btwn background/stopped apps
broadcast receivers: given 10 seconds to handle broadcast events like alarms and arrived-at-location
services: longer-running bg ops
email agents
MUA: client for reading/composing messages, eg mutt
MSA: recv from MUA, send to MTA; most MTAs also MSAs
uses: correct errors (eg missing date/msg-id/domain), simplify MTA policies (refuse mail to non-locals, stricter spam setting)
MTA aka relay: xfer msgs to/from this host; uses SMTP
exim is officially supported on ubuntu; also sendmail (older, famously insecure)
each MTA adds Received hdr
major MTAs (monolithic = less secure)
sendmail: popular, once-insecure, monolithic
exim: monolithic, ok security record, sendmail drop-in
postfix: modular, secure (by sec guy), performant, sendmail drop-in, easy
qmail: secure (by djb), updated in 99, modular, weird to use
http://shearer.org/MTA_Comparison
MTAs have configurable policies/processing; eg postfix can spawn a cmd, pipe to a persistent cmd, etc
port 587 used for MUA-MSA; port 25 used for MTA-MTA; most servers just use 25
MDA: recv msgs to local user, store in inbox; eg procmail (old; most MTAs are also MDAs)
MRA: retrieve via POP, IMAP, etc; eg fetchmail, getmail (python successor)
outlook, thunderbird, etc: MUAs that also have some MSA, MDA, MRA
email configuration
/etc/mailname: the domain of this machine
eg cron jobs are from/to this domain
this is what postfix should set as mydestination
fail2ban: scans logs and bans IPs w too many failed attempts
update iptables FW or tcp wrapper’s hosts.deny
php
php -a: if built w readline support, then behave as console
ruby web app infrastructure
monit: monitors mongrel processes (and unicorn masters)
mongrel: ruby web server
unicorn: request queue for server load balancing; based on mongrel
features 0-downtime app deployment (incremental rollout)
stormcloud: monitors unicorn workers
xen
dom0: hypervisor kernel
domU: guest kernel
hadoop
containers (sources/sinks)
files
SequenceFile: provides len-delimiting btwn keys/values and btwn records
uncompressed, record-compressed, block-compressed
built-in compression codecs: bz2, gzip, default (?)
InputFormat: FileInputFormat:
SequenceFileInputFormat: uses SequenceFileRecordReader
TextInputFormat: uses LineRecordReader
KeyValueInputFormat: tab-separated lines
serialization
Writable: WritableComparable:
hadoop records aka Jute
used in RPC
primitives: byte, bool, int, long, float, double, ustring, buffer
buffer (BytesWritable): use this for eg protobufs
composites: record, vector, map
Record: abstract; for generated classes
DDL (link.jr) to rcc -l C++ or Java
module links {
  class Link {
    ustring URL;
    boolean isRelative;
    ustring anchorText;
  };
}
encodings: binary, CSV, XML
avro
hadoop
used to require keys/values to be Writables, but now uses Serializers and class factories
there’s WritableSerialization
old
RecordReader: also had DBRecordReader
http://code.google.com/p/thrift-protobuf-compare/wiki/Benchmarking
http://archive.cloudera.com/docs/
setup:
scala
vocab
@tailrec
@cps
util.control.Exception.catching
Stream.continually
breakable: somehow faster than simple Exceptions
things i don’t like
complexity, almost entirely in the type system which has many interacting concerns
http://stackoverflow.com/questions/1715681/scala-2-8-breakout
open questions
http://stackoverflow.com/questions/5544536/type-inference-on-set-failing
https://groups.google.com/forum/#!topic/scala-user/NwhaQ6U0wy0
http://stackoverflow.com/questions/6448444/missing-parameter-type-in-overloaded-generic-method-taking-a-function-argument
http://stackoverflow.com/questions/7830731/parameter-type-in-structural-refinement-may-not-refer-to-an-abstract-type-defin
http://stackoverflow.com/questions/7829765/scala-view-bounds-that-work-with-subtypes
http://stackoverflow.com/questions/6849994/understanding-the-interaction-in-scala-between-self-types-and-type-bounds
http://stackoverflow.com/questions/6849137/scala-type-parameter-bound-error-in-subclass-but-not-superclass
collections library was hard to get right (original sucked), and is hard to comprehend
hopefully some clever and motivated minds will make things better in the future
sbt
too hard to get started with (docs are better now)
still too complex to understand and extend
Mark Harrah is difficult and uncommunicative
simple things/extensions are cumbersome:
http://stackoverflow.com/questions/7134993/how-do-i-run-an-sbt-main-class-from-the-shell-as-normal-command-line-program
http://comments.gmane.org/gmane.comp.lang.scala.simple-build-tool/2102
collections branching off from TraversableOnce into Iterator and Seq
bunch of methods missing for TraversableOnce
methods missing unnecessarily between Iterator/Seq, e.g. distinct
syntax
need acute awareness of what we’re dealing with
python 3 gets this right: produce iterators by default, since everything else can be efficient build on top of them
in general, self-type-returning collections are not a good default
for (x <- xs) ... may or may not produce an intermediate list, depending on the type of xs; better remember to always use .iterator
see also the need for breakOut
used to be a die-hard user of Streams, but now almost never use them
for comprehensions and lack of generators:
def splitBy(f: (A,A) => Boolean) = {
  var ys = mut.Buffer[A]()
  for {
    Seq(Some(a),b) <- padIter(xs) sliding 2
    if { ys += a; b == None || f(a,b.get) }
  } yield {
    val oldYs = ys
    ys = mut.Buffer[A]()
    oldYs
  }
vs.:
group = []
for a,b in sliding(xs, 2):
  group.append(a)
  if a != b
    yield group
    group = []
for comprehensions vs python generators/yield
for comprehension syntax is its own little world
nits
: at the end of an operator method means this is the RHS
why aren’t all functions curried? why does currying need to be specified in advance?
abuse of operators: /: is unnecessary
why both classes and case classes? either new Foo or Foo? just have one.
needs to decide whether it wants to integrate with java or not. (don’t!)
:_* syntax
singleton objects are promoted everywhere and are also the main way a lot of tricks in scala are pulled off.
why are for, if, while, etc. special in the language? ideally should be implemented in the language.
breakOut
List.map returns List instead of iterator? (cf. Python)
language is too expansive
Option and null?
XML literals, yet no string interpolation
more serious
too many choices all the time: do I want a function, a view method, etc.
libraries aren’t as mature (complete, battle tested, documented)
lots of compat breaks
IDE is a WIP
syntax just doesn’t flow very smoothly
the infix operator syntax always needs to be wrapped in parens if you then want to apply a unary postfix method, discouraging its use if you want to avoid having to jump around to place parens
the no-() method invocation fails when you want to call an apply on the returned result but the orig method has an implicit param that gets in the way
longer term
no javascript backend
no native backend
no dynamic - Dynamic will come close but not entirely and it’s not here yet
html
link rel canonical: the canonical URL for this page; useful for eg search engines, sharing, bookmarking, etc
glibc
buffering: fully buffered (default), line buffered (tty), unbuffered
see setbuf(3), stdbuf(1)
pdf
http://www.adobe.com/devnet/livecycle/articles/lc_pdf_overview_format.pdf
http://www.mactech.com/articles/mactech/Vol.15/15.09/PDFIntro/
disks
sector: 512 B
partition: physical region on disk; serves as a storage container
windows PC: first partition starts at sector 63
volume: OS abstraction for storage container
usu just a single partition
logical volume managers (LVMs) can create virtual volumes; eg concat or stripe partitions
lvm
features: live PV adds/removes; resize partitions; snapshots; span many disks; RAID0/1
physical volumes (PVs) are real partitions or whole disks
volume groups (VGs) of PVs are logical disks
logical volumes (LVs) are partitions of VGs
can resize LVs; just first resize FS to be same/smaller
reiserfs has fast resizing, unlike ext3
lvm2: read-write snapshots; fragile, don’t be too fancy
default extent (‘chunk’) size 4MB
keeps metadata header at start of each PV; each PV has UUID
complete copy of entire VG layout, incl other PV UUIDs
LVM implemented in terms of the device mapper; simplifies LVM code; all in user space
/boot can’t be in LVM; / not recommended
recommend LVM above RAID
http://www.ntlug.org/Articles/LVM
gdb
mostly implemented using ptrace
athena
system:htaccess.mit: the web server group
random vocab
ILP32: int, long, ptr are 32 bits
LP64: long, ptr are 64 bits
other RDBMSs
main-memory hsqldb: only read uncommitted
mysql
innodb stores data in PK order
explain/optimization
select types
simple: no union or subqueries
primary: outermost select
subquery
dependent subquery: correlated subquery
derived: subquery in from
uncacheable subquery: must re-evaluate for each outer tuple
join/access types, best to worst
const: index lookup yielding exactly 1 row; can turn the result of this index lookup into constant
SELECT * FROM tbl_name WHERE primary_key=1;

SELECT * FROM tbl_name
  WHERE primary_key_part1=1 AND primary_key_part2=2;
eq_ref: ref where the inner table yields exactly 1 row
SELECT * FROM ref_table,other_table
  WHERE ref_table.key_column=other_table.column;

SELECT * FROM ref_table,other_table
  WHERE ref_table.key_column_part1=other_table.column
    AND ref_table.key_column_part2=1;
ref: index join
SELECT * FROM ref_table WHERE key_column=expr;

SELECT * FROM ref_table,other_table
  WHERE ref_table.key_column=other_table.column;

SELECT * FROM ref_table,other_table
  WHERE ref_table.key_column_part1=other_table.column
    AND ref_table.key_column_part2=1;
index_merge: use multiple indexes
unique_subquery: non-dependent (non-correlated) subquery using unique index; turn index lookups into constants
index_subquery: non-correlated subquery using non-unique index; turn index lookups into constants
range: use index to select a range;
index: index scan (per outer row); only if predicate and selected columns are covered by index
all: full table scan (per outer row)
columns
key: the key used by this join/access type
ref: which outer columns are compared against key
innodb
perf
trx_commit, binlog, sync_bin http://www.mysqlperformanceblog.com/2010/02/28/maximal-write-througput-in-mysql/
implements MVCC; has true serializability bc of shared index locks, unlike PG (and future locks or gap locks can be taken on these)
each row has 3 new fields
7-byte ID of last updating (or deleting) txn
7-byte ptr to undo log record (undo log is called the rollback segment)
6-byte monotonically increasing row ID
http://dev.mysql.com/doc/refman/5.0/en/innodb-multi-versioning.html
storage format
if table has int primary key, use that as tuple id; otherwise add tuple id for each tuple
var-len cols
compact/redundant (“antelope”): up to 768B in record (for prefix indexes), rest in overflow page
at least 2 rows + some metadata must fit in each 16KB page, so limit for whole row is ~8KB
dynamic/compressed (“barracuda”): 20B ptr whenever not fit; prefix index can be built separately
each value has exclusive overflow pages; no sharing
monitoring/mgmt
show engine innodb status
show processlist
kill 3 kills process 3 in processlist
clustering
built-in async stmt-/row-based replication
ship stmts/rows/mixed via binary log
log after update completion but before lock release/commit to ensure log is in execution order
mixed: rows automatically used when non-deterministic queries executed
single thread on slave replays log updates
only innodb synchronizes w the binary log
row-based supported by innodb in some cases
auto-inc fields lock table for non-simple inserts
setup/resync of slave is a bit complicated
manual monitoring/failover with stop slave; reset master on promotee and change master to on new slaves
first, wait for stop slave io_thread; show processlist to say “Has read all relay log”
more details: http://dev.mysql.com/doc/refman/5.5/en/replication-solutions-switch.html
google’s 5.5 patches add semisync replication
master confirms receipt & logging of update by at least one slave
only adds wait after commit completes; since commit actually happened, master & slave are actually out of sync (eg if crash during wait, committed txn may not have made it to slave)
mysql cluster: ndb with mysql
ndb is also some sort of standalone clustering solution
ndb supports scalable sync replication & partitioning using 2PC
in-mem; async disk log of redo records and chkpts (2s log write period)
much slower than standard replication
can replicate btwn ndb clusters or btwn ndb & other engines via standard replication
tungsten replicator
simple setup, stmt-based, sync, global txids, cross-dbms
supports mysql, oracle, jdbc; future: postgresql, others
http://www.wikivs.com/wiki/MySQL_vs_PostgreSQL#Replication_and_High_Availability
examples of why postgresql > sql server
http://datachomp.com/archives/top-10-reasons-i-like-postgres-over-sql-server/
examples of why mysql blows vs postgresql
ref: http://now.eloqua.com/e/er.aspx?s=400&lid=235&elq=008b3ac42ba641fb851200ffd6633d90
http://news.ycombinator.com/item?id=2176062
“InnoDB is still broken…Just last week we had to drop/re-create an InnoDB-table in one project because it would not allow to add an index anymore, no matter what we tried…Mysql::Error: Incorrect key file for table 'foo'; try to repair it: CREATE INDEX [...]”
pg now has async replication; that was mysql’s only strong pt against pg
neither has synchronous replication, but pg’s is coming
semisync still unsafe; master waits after actually committing locally, instead of blocking the commit (as it should)
recovery is complex
no group commit http://www.mysqlperformanceblog.com/2011/07/13/testing-the-group-commit-fix/
crappy concurrency, >3 sucks vs pg http://spyced.blogspot.com/2006/12/benchmark-postgresql-beats-stuffing.html
multiple storage engines has always restricted progress: http://www.mysqlperformanceblog.com/2010/05/08/the-doom-of-multiple-storage-engines/
http://stackoverflow.com/questions/324935/mysql-with-clause
http://bugs.mysql.com/bug.php?id=10327
postgresql also supported multiple storage engines in 80s; concentrated on one
mysql’s engine arch is poor; bad to opt/plan/tune
only recently
per-stmt triggers
procedural lang support
only own internal auth system; pg supports a wide array
no referential integrity
no constraints (check)
no sort merge join (certainly no map-reduce contender), let alone hash-join
postgresql has more supple alter table impl
mysql doesn’t support ASC/DESC clauses for indexes http://explainextended.com/2010/11/02/mixed-ascdesc-sorting-in-mysql/
http://www.dbms2.com/2008/07/10/how-is-mysqls-join-performance-these-days/
http://www.mysqlperformanceblog.com/2006/06/09/why-mysql-could-be-slow-with-large-tables/
optimizer only recently started working properly with certain subqueries
auto_increment jumps up to next power of 2 but inconsistently across versions (platforms?); this is undocumented
crappy errors: “Incorrect key file for table ‘stock’; try to repair it” on “alter table stock add constraint pk_stock primary key (s_w_id, s_i_id);” where stock is in InnoDB (which has no “repair table”) means I have no /tmp space (no Google answers)
crappy EXPLAIN output
innodb auto-extends ibdata1 file; only way to trim (garbage collect) is dumping and loading
scoping is broken
mysql> create table t(a int, b int); Query OK, 0 rows affected (3.30 sec) mysql> select a, (select count(*) from (select b from t where a = u.a group by b) v) from t u; ERROR 1054 (42S22): Unknown column ‘u.a’ in ‘where clause’
optimizer sucks
mysql> SELECT distinct t1.tableid FROM transactionlog t1 JOIN transactionlog t2 ON t1.tableid=t2.tableid AND t1.tupleid=t2.tupleid AND t1.partition!=t2.partition LIMIT 100;
+-----------+
| tableid   |
+-----------+
| warehouse |
| district  |
+-----------+
2 rows in set (48 min 15.52 sec)

mysql> SELECT distinct tableid FROM transactionlog GROUP BY tableid,tupleid HAVING count(distinct partition)>1;
+-----------+
| tableid   |
+-----------+
| district  |
| warehouse |
+-----------+
2 rows in set (36.33 sec)
tips
set global general_log = 'ON' or 'OFF'
set session sql_log_off = 1
load data infile 20x faster insert; else, use txns, insert delayed, multi-valued insert
--safe-updates/--i-am-a-dummy: guard against mass deletes/updates that are missing where clauses
grant tables: user, db, host, tables_priv, columns_priv
created by mysql_upgrade and mysql_install_db
“engine” is newer term for “type”
create table t (i int) engine=innodb
--default-storage-engine=innodb
built-ins
myisam: non-txn’l; default; fast
memory: non-txn’l; non-persistent
merge: non-txn’l; read-only
merges identical myisam’s into 1 logical table
insert into physical table; query from merged
innodb: txn’l; foreign key integrity
bdb: txn’l; removed in 5.1
ndbcluster: for mysql cluster; partitioned tables
archive: large data, no indexes, small footprint (compressed)
example: stub dev reference
csv: dev example
blackhole: takes writes but reads are empty
DB tools
mysql2pgsql: pretty good
EnterpriseDB Migration Wizard: migrates mysql to postgresql
failed to handle basic enum type
Source database connectivity info...
conn =jdbc:mysql://wrench.csail.mit.edu:3307/tpcc
user =remotecarlo
password=******
Target database connectivity info...
conn =jdbc:edb://localhost:5432/tpcc
user =yang
password=******
Importing mysql schema...
Dropping Schema: tpcc 
Creating Schema...tpcc 

Migrating Tables for Schema tpcc: 'metarelcloud_transactionlog','metarelcloud_graph','metarelcloud_graphsupport'
The data type enum is not handled in Column querytype of Table metarelcloud_transactionlog
Creating Tables...
Creating Table: tpcc.metarelcloud_transactionlog
Error Creating Table metarelcloud_transactionlog:ERROR: type "enum" does not exist
Creating Table: tpcc.metarelcloud_graph
Creating Table: tpcc.metarelcloud_graphsupport
Created 2 tables.
Loading Table Data in 8 MB batches...
Loading Table: metarelcloud_graph ...
Table Data Load Summary: Total Time(s): 0.014 Total Rows: 0
Loading Table: metarelcloud_graphsupport ...
Table Data Load Summary: Total Time(s): 0.008 Total Rows: 0
Data Load Summary: Total Time (sec): 0.022 Total Rows: 0 Total Size(MB): 0.0
Creating Constraint: PRIMARY
Error Creating Constraint PRIMARY
Creating Constraint: PRIMARY
Creating Constraint: PRIMARY
Creating Index: transactionid
Error Creating Index transactionid: ERROR: relation "metarelcloud_transactionlog" does not exist
Creating Index: tableid
Error Creating Index tableid: ERROR: relation "metarelcloud_transactionlog" does not exist
Creating Index: nodeid
Error Creating Index nodeid: ERROR: relation "metarelcloud_transactionlog" does not exist
One or more schema objects could not be imported during the migration process. Please review the migration output for more details.
postgresql
PG8.3 added spread chkpts; fsync at end
limitations/annoyances
count(*) always scans all rows
indexed fields have ~2.5K size limit
clustering
built-in async streaming record-based replication in 9.0; sync in 9.1
warm standby: can’t be queried; hot standby: can be queried (limited)
manual failover
manual recovery
http://www.postgresql.org/docs/current/static/continuous-archiving.html
http://developer.postgresql.org/pgdocs/postgres/functions-admin.html
http://archives.postgresql.org/pgsql-general/2010-08/msg00264.php
http://momjian.us/main/writings/pgsql/hot_streaming_rep.pdf
pgpool-II
has had async WAL-shipping replication, but this ships WAL segments instead of records
only ships archived WAL segments; controlled with eg archive_timeout
lag is on order of minutes; uses commands like scp or cp
to check for stale reads, can compare pg_current_xlog_location() and pg_last_xlog_replay_location()
slony: built by jan weick, postgresql core team member; main option
much slower/uses more resources than mysql built-in replication
O(n2) communication costs
sql-/trigger-based replication
auto failover
2PC: prepare transaction, commit prepared, rollback prepared
pgpool-II
synchronous replication, auto failover, connection pooling/load balancing, online recovery
sync replication: 30% write overhead, avoid non-pure queries
9.0 async replication: provides auto failover, conn pooling, load bal, etc.
pgbouncer: simple, lightweight connection and transaction pooling
skytools
pgq: efficient transactional queue
londiste: async replication via pgq; cross-version support
plproxy: partitioning/rpc
WAL
segments are separate 16MB files
checkpoints taken by default every 3 WAL segments or 5m
has async api
monitoring/introspection
select * from pg_stat_activity: see current queries/activity
select * from pg_locks where not granted: see locks
rules: query rewriting, eg let you insert/update a view and redirect to underlying table(s)
triggers: executed in-transaction
notify/listen: messaging to clients
sent only after commit
received only when not in txn
like unix signals, many redundant signals can be reduced to one, and can’t carry extra data
can use rules to generate
on EBS, run CREATE TABLESPACE ephemeral LOCATION '/mnt/postgresql_tmp'; and config temp_tablespaces = 'ephemeral' http://www.chrisstucchio.com/blog/2011/speed_up_postgres_ec2.html
DWARF2
unwind tables
-fasynchronous-unwind-tables: table is exact at each instr boundary, so can be used to unwind from async events (debugger, GC)
even when -fomit-frame-pointer
put in ELF section .eh_frame_hdr; used by gdb, backtrace() (even though docs say -fomit-frame-pointer breaks it)
not stripped; not a normal debug section, but an allocated data section
must have accurate tables through 100% of binaries, eg libc
http://www.yosefk.com/blog/getting-the-call-stack-without-a-frame-pointer.html
networking
4.2.2.1: level3 ns server
quilt: manage stack of patches outside of a VCS; these just “hover around”; mq in hg, stgit in git; git rebase is superior; workflow: update to new package then reapply patch stack, resolving conflicts 1-by-1
cd some_existing_hg_repository

# setup the patch queue directory (Deprecated in 1.5)
hg qinit

# create a new patch named firstpatch
hg qnew firstpatch

# edit some files
vi filename

# update the patch to contain your changes
hg qrefresh

# vi .hg/patches/firstpatch to see the result
# print the current patch to the screen
hg qdiff

# make some more changes
vi filename

# see the differences not yet stored in the patch
hg diff

# update the patch
hg qrefresh

# create another patch
hg qnew secondpatch

# Make more changes, and update the new patch
vi filename
hg qrefresh

# Look at the patches you have applied
# Look at all the patches in the queue
hg qapplied
hg qseries

# remove the top patch
hg qpop

# apply the patch again
hg qpush

# remove all patches
hg qpop -a

# apply all patches
hg qpush -a
git
# push your new locally created branch to remote
git push origin origin:refs/heads/new_feature_name
# verify
git branch -r

# publish history to svn (note the `trunk` dir)
svn mkdir protocol:///path/to/repo/PROJECT/trunk -m 'importing git'
git svn init protocol:///path/to/repo/PROJECT -s
git svn fetch
git rebase trunk
git svn dcommit

# equivalent to svn export
git archive master | tar -x -C /somewhere/else/
git checkout-index -a -f --prefix=/destination/path/

# bundle up a whole repo (for sending)
git bundle create repo.bundle master
# clone from bundle (rather than server)
git clone repo.bundle -b master repo

# bundle up some commits
git bundle create commits.bundle master ^9a466c5
# fetch from bundle (rather than server)
git fetch ../commits.bundle master:other-master

# revert a file
git checkout FILE
git checkout -- FILE # if FILE has same name as branch

# replace one commit in your hist
git replace 81a708d c6e1e95

# add another origin
git remote add origin ssh://...

# undo last commit (implies --soft)
git reset HEAD^
# change the last commit; also: git reset --soft HEAD^, ..., git commit -c ORIG_HEAD
git commit --amend
# choose which commits to amend and then replay (step through) them
git rebase -i
# force origin to accept your rebased changes
git push -f
# fetch into new branch on the fly
git fetch file://another/repo +master:temprepo/master
# just mirror another repo, discarding all your current changes
git reset --hard temprepo/master
# split subdir into new repo without losing history
git filter-branch --prune-empty --subdirectory-filter SUBDIR master
# stash away current changes and then retrieve them
git stash
git stash apply
# make named stash, view stashes, and apply last one
git stash save 'Description of my changes'
git stash list
git stash pop
# create a new branch NEWBRANCH (based on SOURCEBRANCH)
git checkout -b NEWBRANCH [SOURCEBRANCH]
# create an archive of the repo from tree 'COMMIT'
git archive --format=tar --prefix=foo-1.1/ COMMIT | gzip > foo-1.1.tgz
in merge, git show :1:file selects ancestor, :2: selects ours, :3: selects theirs
porcelains: easygit, yap, pyrite (hg-like)
GCs after 1 month
working with multiple projects
submodule: foreign repo (fixed commit) embedded in a dedicated subdir
like svn externals, but can’t track HEAD; must explicitly update
the tracked versions is a data file that itself is tracked in the master git repo, so concurrent changes to it result in conflicts
subtree merge: actually merge in another proj but isolated in its own subdir
repo: google’s tool; works with gerrit; expects workflow of creating topic branches and code reviews
braid
http://thread.gmane.org/gmane.comp.version-control.git/148034
name resolution on linux
/etc/nssswitch.conf indicates what /lib/libnss* to use for resolving certain classes of names
libnss_compat: uses NIS/NIS+, DNS, files (/etc/{hosts,networks})
linux has no resolver daemon or kernel code for resolving names (i.e. no caching); all in lib
.NET 4
BCL: code contracts, parallelism, tuples, better fire IO
mscorlib: main .NET runtime lib (.NET used to be called Common Object Runtime)
common C defines
http://www.woodtennis.com/win2unix.html
#ifdef _WIN32 windows dependant code or header filess ... #endif
#ifdef _WINAPI windows API dependant code or header files ... #endif
#ifdef _ISAPI windows Internet Server API code or header files ... #endif
#define __cplusplus c++ specific code
#ifdef _ cplusplus extern "C" { // ((Start all the declarations for "C" s/w ...)) #endif
#ifdef _ cplusplus } // ((End all the declarations for "C" s/w ...)) #endif
#undef __cplusplus c++ specific code
#define EC_SUCCESS 0 maps unix error code to NT version ...
#define EC_ACCESS EACCES maps unix error code to NT version ...
#define _Win_DLL dll object needed only by windows NT apps
#undef _Win_DLL dll object needed only by windows NT apps #endif
#ifdef _Win_DLL dll objects not needed for unix programs ... #endif
#ifdef _KERNEL_PROVIDED_DLL_ // kernel exports dll's for application
# define _ KERNEL _DLLSPEC_ __declspec(dllexport) // kernel code exports dll's
#else
# define _ KERNEL _DLLSPEC_ __declspec(dllimport) // applications import dll's
#endif // _KERNEL_PROVIDED_DLL_
Other recommended define usage include machine/compiler : _MSDOC, _MACOS, _HP, _SUN, _LINUX, _SOLARIS, etc ... inorder to make the same source as portable as possible
Avoid using COM/DCOM use CORBA or even better SOAP & XML to do distributed objects & marshalling
i18n
Unicode
1.0: 1991; 16 bits; UCS-1, UCS-2, UCS-4
2.0: 1996; 21 bits; UTF-8, UTF-16, UTF-32
Unicode is basically identical to Universal Multi-Octet Coded Character Set (UCS or ISO/IEC 10646)
but UCS goes up to 31 bits instead of 20.1
the two standards weren’t always the same — in the 90s, there was a lot of disagreement between the computer companies who backed Unicode and proposed a simple 16-bit character set, and the standards mavens behind UCS, who wanted to accommodate more languages than 65,536 characters would allow. The two sides eventually compromised on the current 20.1-bit character set, but some historical differences still linger between the two “official” specifications
http://blog.ksplice.com/2010/03/longest-tweet/
ASCII: 7 bits
ISO/IEC 8859-1 aka Latin-1: superset of ASCII; 191 chars
ISO-8859-1: superset of ISO/IECS 8859-1
combines with ISO 6429 control chars (“C0 and C1 control chars”)
assigns control chars to 00-1F, 7F, 80-9F
Windows-1252: superset of ISO/IEC 8859-1
uses 8 bits; assigns 80-9F displayable chars (not control chars)
UTF-7: for encoding unicode in ascii, eg in email; security issues
UTF-8: superset of ASCII; everything else -> multibyte
00-7F (7 bits): 0_______
0080-07FF (11 bits): 110_____ 10______
0800-FFFF (16 bits): 1110____ 10______ 10______
010000-10FFFF (21 bits): 11110_ 10____ 10______ 10______
because “continuation bytes” are 10______, easy to start reading from middle of a file by looking for next non-continuation byte
http://research.swtch.com/2010/03/utf-8-bits-bytes-and-benefits.html
http://intertwingly.net/stories/2004/04/14/i18n.html
UTF-16: still multi-byte (“surrogate pairs”); requires BOM
x86 calling conventions
http://unixwiz.net/techtips/win32-callconv.html
http://en.wikipedia.org/wiki/X86_calling_conventions
windows and non-windows
cdecl/intel ABI
args pushed in right-to-left order
return values in eax
eax, ecx, edx avail for use within function
stack cleaned up by caller (esp incremented by caller)
MS compilers prefix these function names with underscore
this is the default for MS compilers
main/wmain, variadic (even if declared otherwise), higher-order functions
windows
stdcall
stack cleaned up by callee; no need to add cleanup to every callsite
windows api uses this; see WINAPI macro
can’t handle variadic functions like printf
MS compilers prefix these function names with underscore and suffix them with @ and number of bytes of params
WinMain
(MS) fastcall
uses registers
MS compilers prefix these with @, suffix with @ and param byte count
non-windows
syscall
eax, ecx, edx not preserved
size of param list passed in %al
caller cleanup
optlink: TODO
MS x64: uses R registers; supercedes all 32-bit conventions
amd64 ABI: uses R registers
uuid/guid (syn): based on mac (ieee 802) addr, so can get unique numbers without any communication
mocks vs stubs (vs fakes)
all called test doubles
fake: working implementations, but have shortcuts that make them unsuitable for production (e.g. in-memory database)
stub: provide canned answers to calls made during test; may also record info
mock: objects pre-programmed with expectations which form a specification of the calls they are expected to receive
networking
linux doesn’t have a dns cache by default; need to install nsc for that
linux power mgmt
acpid: delivers ACPI events; see /etc/acpi/
put to sleep: echo -n mem > /sys/power/state
xdr vs. asn.1
abstract syntax notation (ASN.1): ITU
packed encoding rules (PER): more compact than BER
basic encoding rules (BER)
voip
SIP: extensible; won VOIP wars; IETF std; builds on other standards
H.323: always back-compat; ITU standard; ASN.1 PER
net lib
getsockname getpeername
inet_ntoa inet_aton
even-lesser-known Linux syscalls
splice
tee
vmsplice
lesser-known Linux syscalls
mprotect
mmap
shmat shmget shm…
Hotspot
exceptions much faster than on CLR: http://weblog.ikvm.net/PermaLink.aspx?guid=c84a5b22-d633-4eeb-a8e1-244ff66161a2
object layouts
objects 64-bit aligned, have 2-word header (word = native ptr size); other VMs may have 3
fields word-aligned
headers
objs: class ptr, flags
arrays: class ptr, flags, size
flags: incl hash, GC
http://java.sun.com/developer/technicalArticles/Networking/HotSpot/
useful flags
-verbosegc
-XX:+UseCompressedOops: many 64-bit refs now 32-bit; usable below 32GB max heap; enabled by default below 30GB max heap
-Xss<SIZE>: stack size; useful for many threads (1024KB on 64-bit Linux)
From http://java.sun.com/products/hotspot/whitepaper.html
In the Java HotSpot VM, no handles are used by Java code. Object references are implemented as direct pointers. This provides C-speed access to instance variables. When an object is relocated during memory reclamation, the garbage collector is responsible for finding and updating all references to the object in place.
raw pointers not too bad to update since you must traverse all objects anyway
they probably use the mark word to redirect to the new location
objects copied in DFS; this clusters related objects together
vocab
ordinary object pointer (OOP)
compressed OOPs: in Java 7; 32-bit ptrs
wide_oop = narrow_oop == 0 ? 0 : (narrow_oop << 3) + base
allows up to 4B objs/32GB
Each object has special first word for synchronization, marking, hashing
First word of each object is the mark word
Used for synchronization and GC
Also caches hashcode, if previously computed
from http://www.ibm.com/developerworks/java/library/j-benchmark1.html
blocks need to execute 1500 (client) or 10000 (server) times before they’re JITed
compile whole method
on-stack replacement: can replace with compiled method in middle of a call to it (eg if loops in main)
however, code quality suboptimal: can’t do loop-hoisting, array-bounds check elimination, or loop unrolling
only way to defeat this: move tight loops into their own methods
code can be deoptimized if assumptions made during JIT no longer hold
eg class loading that invalidates monomorphic call transformations
eg uncommon traps: only the most common code path is compiled (like in tracing JIT?), but turns out to be uncommon
dead code elimination
memory optimizations
hoisting heap values into registers
stack allocation
object eplosion
GCs
throughput collector: generational, parallel, compacting
parallel GC: actually stop-the-world on “young generation”
“parallel” means implemented w threads for better speed
ConcurrentMarkSweep (CMS) GC = “Full GC”
apart from initial mark and final cleanup phase, it is really concurrent, meaning that it runs in parallel to the normal process
short incremental parallel STW pauses
doesn’t compact, prone to frag/STW pauses
G1: like CMS, try to avoid STW pauses w short incremental parallel STW pauses, but does compact to avoid frag
adaptive sizing policy: self-tuning
eg -XX:MaxGCPauseMillis=100, -XX:GCTimeRatio=19
monitoring, management, instrumentation
no startup flags: no cpu sampling possible
remote apps: no mem sampling possible
startup flags (default JDWP port 1044):
-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=1044
startup flags (default JMX port is 9090):
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port=3000
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
JVMTI: replaces JVMPI, JVMDI
JPDA
JDWP
JDI
jdb uses ?
JDWPI
JConsole
VisualVM
jstat
JMX
MBeans
JMX:MBeans::SNMP:MIB
http://blogs.oracle.com/jmxetc/entry/simple_is_not_easy
architectural map: http://download.oracle.com/javase/7/docs/
servers
tomcat: servelt, JSP
full JEE app servers: jboss, websphere, glassfish
alignment
posix_memalign: useful for aligning to cache line sizes
sockets
on linux, default: SO_RCVBUF = 87380, SO_SNDBUF = 16384
non-blocking IO quirks
sockets that are accepted from a non-blocking listener are blocking
EAGAIN == EWOULDBLOCK == 11
From select(2):
Under Linux, select() may report a socket file descriptor as “ready for reading”, while nevertheless a subsequent read blocks. This could for example happen when data has arrived but upon exam‐ ination has wrong checksum and is discarded. There may be other circumstances in which a file descriptor is spuriously reported as ready. Thus it may be safer to use O_NONBLOCK on sockets that should not block.
From section 13.2 of the manual:
EAGAIN
Normally, when no input is immediately available, read waits for some input. But if the O_NONBLOCK flag is set for the file (see File Status Flags), read returns immediately without reading any data, and reports this error.
listen actually accepts connections (based on the backlog argument)
accept just takes one of the already-accepted connections
inotify: Linux’s filesystem change notification facility
obsoletes dnotify
difficult for efficient (real-time) detection due to coarse granularity (directory level)
tcp wrapper (TCP_WRAPPERS)
libwrap: just the library
openssh’s sshd uses libwrap
/dev/shm/: tmpfs (in-memory)
strace more comprehensive than ltrace
Collada DAE files: standard 3D model interchange format established by the Khronos group (established at SIGGRAPH 06 to maintain OpenGL)
status of AIO
From http://davmac.org/davpage/linux/async-io.html:
A more subtle problem with non-blocking I/O is that it generally doesn’t work with regular files (this is true on linux, even when files are opened with O_DIRECT; possibly not on other operating systems). That is, opening a regular file in non-blocking mode has no effect for regular files: a read will always actually read some of the file, even if the program blocks in order to do so. In some cases this may not be important, seeing as file I/O is generally fast enough so as to not cause long blocking periods (so long as the file is local and not on a network, or a slow medium). However, it is a general weakness of the technique.
…
The select() function is documented in the libc manual. As noted, a file descriptor for a regular file is considered ready for reading if it’s not at end-of-file and is always considered ready for writing (the man page for select in the Linux manpages neglects to mention both these facts). As with non-blocking I/O, select is no solution for regular files (which may be on a network or slow media).
From http://www.mail-archive.com/libevent-users@monkey.org/msg01226.html:
I wouldn’t expect AIO to knock your socks off on Linux (and certainly not *BSDs). Kernel support doesn’t exist for AIO operating through the disk buffer cache. Linux only supports AIO for direct I/O, which is extremely sensitive to myriad system and usage scenarios. Otherwise, it’s still emulated w/ threads (in glibc, I think).
From Linux System Programming:
Linux only supports aio on files opened with the O_DIRECT flag.
samba/smb
new way of sharing doesn’t modify /etc/samba/… but instead modifies /var/lib/samba/…
shares are in /var/lib/samba/usershares/
can either use smbpasswd to maintain a set of users, or uncomment the security = user line in smb.conf
smbd can be controlled using e.g. smbcontrol smbd [reload-conf | shutdown | ...]
in recent ubuntu, nautilus has gui for sharing
posix asynchronous i/o (AIO) different from non-blocking IO (NBIO)
NBIO can be done on network conns
AIO can be done on disk files, but only reads and writes
see also: LAIO
Duff’s device: a way to unroll loop
idea be used to implement (very limited) coroutines
Functional Pearl: Power Series, Power Serious
power series for math (ex, sinx , cosx , derivatives, integrals, and more)
An Efficient Representation for Sparse Sets
maintain a packed list of members and a sparse indexing array
sparse array does not need to initialize memory
byte ordering
freebsd’s byte order ops
glib’s byte order macros
cooperative threading and coroutines for c/c++
st http://state-threads.sourceforge.net/
high performance
uses longjmp/setjmp
no extraneous features
timeouts on IO
no pthread interop
verified that it works with gprof, gdb; doesn’t work with valgrind
Pth
compat with pthreads
uses longjmp/setjmp
TODO pthread interop?
libtask
no users, sparse documentation
no timeouts on IO
small and simple
uses getcontext/setcontext
no pthread interop
tame
each tamed function call results in a malloc to save state
nice in the sense that you can compose multiple events
pthread interop
boost.coroutine
TODO pthread interop?
glitchy, not ready
uses longjmp/setjmp
does not hide asio behind a synchronous interface, but instead TODO
has a wait() that can wait for multiple tasks, but there is a note that this is not a general-purpose demuxer, and is only suitable for things like setting timeouts on an asio operation
TODO understand the above better
capriccio
TODO
c/c++
macros
STDCFORMATMACROS
STDCCONSTANTMACROS
STDCLIMITMACROS
uint_least*_t
uint_fast*_t
__STDC__: ANSI C or C++ (vs. K&R C)
__cplusplus: C++
reference rules
const T&: binds to anything
T&: binds to non-const lvalues
const T&&: binds to anything
T&&: binds to non-const lvalues and rvalues
lvalues: addressable/reference-able; named; can be modified
rvalues: const-reference-able; moveable in c++0x
volatile vs. atomic
src: http://www.ddj.com/hpc-high-performance-computing/212701484
volatile: unoptimized; “unusual memory”
not atomic
ordinary memory optimizations: some: loads may still be reordered across volatile load/store
special operation optimizations: none
atomic: “ordered atomic”
all-or-nothing stores (volatile in Java/.NET)
ordinary memory optimizations: some: loads can be moved down across an atomic load or up across an atomic store
special operation optimizations: some, eg combining adjacent stores to same location
macros: http://bytes.com/forum/thread521533.html
lvalues and rvalues: http://www.devx.com/tips/Tip/5696
sizeof wchar_t == 4
no way to use anything but default ctor in array
i.e., none of this: new vector(256)[10];
“Each name that contains a double underscore (_ _) or begins with an underscore followed by an uppercase letter (2.11) is reserved to the implementation for any use.” ref
all *_t type names are reserved by posix; ref
stateful allocators are not allowed by the standard (which seems to make them useless), but most impls waive this part ref
lexical cast: to/from strings
dtors reverse order of ctors
casting through unions gets around strict aliasing
bugs
precedence: for (int i = 0; i < multimode ? nnodes : 1; i++)
javascript implementations
started evaluating the engines in trying to run the JS beautifier
tamarin
JIT engine donated by Adobe to Mozilla
used in Flash 9
written in C++
separate compiler (asc) and executor (avmplus)
the shell module contains a number of system/IO facilities (toplevel.as)
“Tamarin was designed to do better with type annotations than without. This is why SpiderMonkey actually wins some of those benchmarks against Tamarin on untyped code. Our work with SpiderMonkey and Tamarin now in the ActionMonkey project aims to make it fast for untyped code as well.”
rhino
written in Java
spidermonkey
written in C++
help()
load() includes javascript files
readline() doesn’t really work that great, since it
arguments for command-line args
es4
reference implementation of ECMAScript 4 (Javascript 2)
written in SML
very slow; it actually times out when running the beautifier (I get “Alarm Clock”)
c++ utility library documentation
libstd: algorithm, dynamic_bitset
http://gcc.gnu.org/onlinedocs/libstdc++/latest-doxygen/
http://www.boost.org/doc/libs/1_40_0
ptrs, concepts, optional, variant, any, io, format, date time, utility (noncopyable, base_from_member), format, range
sys: fs, threads, asio, program_options, system (errors)
lesser: spirit (safe_bool), tribool, value_init
range ex
typedef iterator_range<list<int>::const_iterator> range_t;
range_t it = make_iterator_range(xs)
BOOST_FOREACH (range_t::reference s, it) ...
build on win: bjam --build-type=complete --with-program_options toolset=msvc-10.0
http://pocoproject.org/docs/
ByteOrder, UnicodeConverter
sys: DNS
http://stlab.adobe.com/asl_toc.html
forest, move, COW, table index, adam/even gui
http://doc.trolltech.com/4.5/
http://api.kde.org/
http://wxwidgets.org/
http://dclib.sourceforge.net/
http://www.uplinklabs.net/?page=crisscross/index
http://www.gnutelephony.org/doxy/bayonne2/
http://libutl.sourceforge.net/
C utility library documentation
http://apr.apache.org/
http://libclc.sourceforge.net/
http://dklibs.sourceforge.net/doxy/html/index.html
https://sourceforge.net/projects/libul/
c++
#define public private trick may allow you to access private variables
extern "C": preserves C calling convetions and name (non-)manging
streams: clog = cerr
iostream synced w cstdio; see sync_with_stdio()
rdbuf gets underlying streambuf
ranges
advantages of ranges over iterators
iterator_range(begin, end): iter -> range
iterator_traits::type: range -> iter
begin(), end()
transform
make_filtered_range, make_transformed_range; or, input_range | transformed(f) | filtered(f)
iterators
make_filter_iterator, make_transform_iterator
transform
pains
C++ is not CFG
“most vexing parse”: coined by scott meyers in “effective stl”
S s(S()); is interpreted as fn decl not var copy-ctor decl
a function that takes (a function that takes 0 args and returns S)
also, S s(); is a fn decl
verbosity: vector<string> xs; f(xs, ...) vs. vector<string> xs = f(...)
idioms/patterns
Curiously Recurring Template Pattern
optimizations
return value optimization (RVO)
named RVO (NRVO)
empty base optimization (EBO)
c++0x
is an upgrade to tr1
shared_ptr
hash_map
tuple
concurrency
memory model
static init
async: futures, promises
atomic
auto
rvalue refs: moves, forwards
variadic templates
lambdas
uniform init syntax, init lists
regex, regex literals
dropped: concepts, GC
g++
warnings
-Wall -Wextra -Werror -Woverloaded-virtual -Wconversion -Wpointer-arith -Wcast-qual -Wcast-align -Wwrite-strings -Wno-sign-compare -Wno-unused-parameter -Wold-style-cast
-Wshadow: This warns when you hide a variable by defining one in a narrower scope.
-Weffc++: This warns about things found in the “Effective C++” books. Some are useful, but many are not.
linking
gold: new gnu linker that is parallelizable
COMDATs: for vague linkage
constructs that don’t clearly live in a single place have vague linkage
eg for C++ constructs: header inlines, vtables, typeinfos
there must be only one in the final linked program, so that you can take its addr consistently
COMDAT sections: each has special string; linker keeps only one of each string
http://www.airs.com/blog/archives/52
java
visualvm: sampling profiler in 1.2+
jstack: print thread stacks of a running process or core file
interface fields are all static
initial heap size = 1/64 phy, max heap size = 1/4 phy (cap 1gb)
System.out, System.err are PrintStreams that by default auto-flush; they don’t use C standard streams
unsigned comparison: (i < j) ^ (i < 0) ^ (j < 0)
annotations
GuardedBy(“this”): document thread-safety
TODO: does volatile make guarantees about the atomicity of say 64-bit wide values?
volatile: ordered atomic types
like c++0x atomic
commonly used for flags
also more efficient than AtomicReference when instantiating many of something; use in conjunction with Atomic*FieldUpdater
accessing these creates a memory barrier, effectively synchronizing all cached copies of variables with main memory
enables correct double-checked locking
these are atomics; from http://stackoverflow.com/questions/967124/java-seems-to-support-volatile-fields-of-type-long-while-c-does-not-what-are > When a double or long in Java is volatile, §17.7 of the Java Language > Specification requires that they are read and written atomically. When > they are not volatile, they can be written in multiple operations. This > can result, for example, in the upper 32 bits of a long containing a > new value, while the lower 32 bits still contain the old value.
atomic field updaters
reentrancy
aka “purity”; output depends only on inputs (and static constants); no static state is changed (no side effects)
an interrupt handler is reentrant if it reenables interrupts early on
implies thread safety
thread safety
ways to realize:
reentrancy: state is private per caller (eg on stack)
mutual exclusion: can update static state, but serialize accesses
thread-local storage: each thread has private copy of statics
atomic operations: using special atomic machine instructions; lock-free
aka linearizability
c/c++ libs
raise
sigaction
backtrace
backtrace_symbols_fd
c
the declaration f() is not the same as the declaration and prototype f(void)
printf flags
%td: ptrdiff_t
%zd: size_t
random() is “supposed to be more random” than rand() (though mostly the same these days)
c/c++ tools
lzz
doxygen can’t parse (eg) lazy classes
lzz can’t copy comments into gen files
sql
recursive queries are not in pgsql, are in db2, maybe sql server
on the roadmap for pgsql: http://archives.postgresql.org/pgsql-hackers/2007-11/msg01083.php
select into is dead; long live create table as
elf
.text
.plt: stub code that helps to resolve absolute function addrs
.gotplt: absolute function addresses; used by .plt entries
.debug_info
.gnu_debuglink: checksum and filename for debug info
.dynsym: maps exported symbol names to offsets
.dynstr: stores exported symbol name strings
.symtab: maps symbol names to offsets
.strtab: symbol name strings
gcc
-march, -mtune, -mcpu:
On x86 and x86-64 CPUs, -march will generate code specifically for that CPU using all its available instruction sets and the correct ABI; it will have no backwards compatibility for older/different CPUs. If you don’t need to execute code on anything other than the system you’re running Gentoo on, continue to use -march. You should only consider using -mtune when you need to generate code for older CPUs such as i386 and i486. -mtune produces more generic code than -march; though it will tune code for a certain CPU, it doesn’t take into account available instruction sets and ABI. Don’t use -mcpu on x86 or x86-64 systems, as it is deprecated for those arches.
-march=native: new option since gcc at least 4.3.2
attribute: general metadata attached to funcs, vars, etc.
__attribute__ ((constructor)): have a function get run before main
inline: force inline
XXX: same as always_inline?
static __inline __attribute__((always_inline, no_instrument_function))
global offset table (GOT): in PIC, constants are accessed via this; resolves the GOT entries by OS’s dynamic loader
procedure lookup table (PLT): used to resolve procedure call relocation placeholders; used alongside GOT in ELF
symbol visibility
default visibility: importing exe can override functions referenced by lib; useful for eg changing behavior of malloc
hidden/internal visibility: not visible to outside
protected visibility: visible to outside, but internal references use lib’s def’n
slower at link time due to C requirement that ptrs to same function must be equal
ld
-l order matters: dependents before dependencies (e.g. -l should follow sources)
compilation
multilibs: multiple versions of libraries for different target options (for same general architecture)
eg: -m32, -msoft-float
gcc -print-multi-lib
matlab
useful vectorization idioms
repmat(reshape())
make
:= means immediate expansion; = is like function definition
automatically removes intermediate files at the end
eg, I had: %.cpp: %.lzz; make removed the cpp
more info
image conversions
convert doesn’t deal well with svg
svg to *: inkscape
bibtex
multiple authors must be joined with “and”: “Yang Zhang and Sam Madden”
{} serves many purposes
outer structure (obvious)
quotes around attribute values, same as “”
grouping within attributes, eg “John {von Neumann}”
tex/latex
:
TODO
how do labels work?
how do def/let/setbox compare?
when can i use = sign?
fonts
taxonomy
bitmap fonts
type 3: eg, dvips converts to type 3 the metafont bitmaps of knuth’s original “computer modern”
outline fonts: chars are sets of bezier curves
type 1 (postscript)
defined using actual postscript, but a restricted subset
most modern tex systems default to type 1 (source)
examples
orig. adobe fonts: times, palatino
type 1 computer modern, avail. in the BaKoMa font collection
more exotic: sabon
truetype
opentype
marriage of type 1 and truetype
advanced typographic features
supports unicode
both pdf and ps can embed type 3
some sources say tex uses type 3 (bmp) fonts by default; others say recent latex don’t default to type-3
TODO difference btwn fonts, font encodings, etc.
OT1: original LaTeX font encoding
defaults to CM font
T1: new LaTeX font encoding
defaults to EC font (same as CM but with more glyphs)
use ae, aecompl ((source)[http://dsanta.users.ch/resources/type1.html])
ae auto-sets fontenc to T1
force usage of type 1/truetype (scalable) fonts
to use Computer Modern (CM)/Extended Encoding (EC), add:: ::
to use standard PS fonts:: ::
quirks
verbatim doesn’t work within… (source)
latex new font selection system (NFSS)
primitives: fontencoding, fontfamily, fontseries, fontshape, fontsize, selectfont
attributes
series: textmd, textbf
family: textrm, textsf, texttt
texttt: only hyphenated where allowed with -
shape: textup, textit, textsl, textsc, textnormal
mathrm: upright font, math spacing
source
modes
paragraph: wrapping
math
left-to-right (LR): no wrapping
boxes
mbox: LR mode; auto width
vs in math: sizes better (eg if your text is subscript, will be smaller, will be inflated)
ref
provided by amsmath
fbox: mbox + frame
makebox: mbox + specify width, text placement
parbox
others: sbox, framebox, dashbox, hbox
general programming
dtx files: literate sources; stripped before deployment
def: late expansion (function definition)
newcommand: safe def (no clobbering)
let: immediate expansion; no args
dimensions
use setlength on eg baselineskip, parindent, etc
skips (baselineskip, parskip) consist of 3 numbers: amount, shrinkability, stretchability
use renewcommand for baselinestretch; can’t use setlength because it’s not a dimensional quantity
relax: stop consuming into an expression (eg don’t consume following plus or minus)
rubber
may need to manually activate modules eg -m bibtex, if (say) somewhere before that the scanner encounters \end{document} wrapped in a never-satisfied \if
math
DeclareMathOperator gives the right superscript/subscript behavior
operatorname is the anonymous version of this
graphics
epsfig: superset of graphicx
source
layout
figure*, table*: float across columns
fullpage: simple way to use full page layout (instead of the highly narrow default)
text formatting/layout
\centerline, center env
\hfill
\vskip
ps/pdf
conversion to pdf
epstopdf (texlive)
ps2pdf -dPDFSETTINGS=/prepress: a bundle of settings that optimize for publishing (i.e., highest-quality); eg embed fonts, prevent downsampling of EPS images
-d sets a setting to a value, -s sets to a string
alternatively, specify these options in GS_OPTIONS; this is useful when ps2pdf is indirectly invoked, e.g. via rubber
other settings include /default, /screen (low-res), and /printer
conversion to ps
pdf2ps (gs): ignores the PDF MediaBox and finds tightest BoundingBox
source
convert (imagemagick): identical (by default) to pdf2ps, since im uses gs
pdftops (xpdf): seems to yield higher quality by default, but not sure why, or whether it’s perfect
misc tools
pdfinfo (xpdf)
Document Structuring Conventions (DSC): all those standard comments you see are part of this thing, established by Adobe
dvips and dvipdfm
haskell
profiling and threading not support
for profiling, need to specify ghc-opts: -auto-all (or else specify your own “cost-centers” using those scc annotations)
TODO space leaks
cabal install --global
profilers
gprof can handle multi-threaded apps, but doesn’t play well with -lprofile
gprof requires -pg to be sent to both linker and compiler
TODO: how does gprof work, exactly? it seems to have a sampling part for profiling (finding where % time is spent), but also an instrumenting part for setting up the stack traces for profiling and for counts
google perftools cpu profiler does not like x86_64 (google for issues)
posix
FS
atime: originally when file was last read-accessed; now either disabled (noatime) or only updated if atime<mtime (relatime) for tools that need to know if file was accessed since modification
mtime: when data was changed
ctime: when data or metadata was changed
IEEE Std 1003.1 aka POSIX.1
daemonizing
http://www.enderunix.org/docs/eng/daemon.php
http://www.itp.uzh.ch/~dpotter/howto/daemonize
http://code.activestate.com/recipes/278731-creating-a-daemon-the-python-way/
http://0pointer.de/lennart/projects/libdaemon/
http://www.developerweb.net/forum/showthread.php?t=3025
signals
synchronous: SIGSEGV, SIGFPE, …
asynchronous: SIGUSR1, …
processes
a process that has exited, but whose parent has not called wait()/waitpid() on it, is a zombie
unless the parent explicitly ignores SIGCHLD
pid’s can be reused, but running and zombie pid’s are unique (kill’s semantics demand it)
pid 0: idle task, never exits; pid 1: init task (calls wait periodically to sweep up zombies)
orphan processes are adopted by init
process groups
used to control signal distribution
session: group of groups; procs can’t join/create groups in other sessions
used to implement login sessions
session leader: proc that interacts with the controlling terminal (shell); terminates procs on hang-up
exit codes
mod 256
convention: 0 = success, 1 = failure, >=128 = subprocess failure
139 = seg fault = 128 + (11=sigsegv); however, bash’s “Segmentation fault” is not determined by this
wait(2) gives you other info, eg whether program crashed (presumably bash uses that)
no way to capture shell’s “Segmentation fault” message to a file, AFAIK
time
ctime() produces time_t, 32-bit seconds since 1/1/70 UTC
tricks
zygote process: a technique for being able to exec the same program later, even if binaries are replaced on disk
do this by keeping a prototype process around since startup and forking it when needed
http://code.google.com/p/chromium/wiki/LinuxZygote
terminals
hierarchy: each process in one process group; each group in one session
each session may have one controlling terminal
processes may open/talk to any number of terminals, but special behaviors for job control and access control apply only to controlling term
generally, all processes in a single login session or a single terminal emulator session are in the same session
each terminal has exactly 1 foreground process group; others are background
termios is the name of the system; while bash uses libreadline, termios provides the (much poorer) line editing facilities when using (eg) cat
http://www.linusakesson.net/programming/tty/index.php
http://blog.nelhage.com/2010/01/a-brief-introduction-to-termios-signaling-and-job-control/
automake
SUBDIRS order matters
libtool
PIC, ranlib, shared+static, convenience libs
should always use libtool/LTLIBRARIES
build systems
autotools
the king; should try to at least get a taste of this
ultra-portable: essentially contains a DB of platform APIs and known incompatibilities (eg autoscan will warn about utime() usage)
total nightmare, esp. ultimately requiring you to write portable shell scripts (for autoconf)
ideally, a higher level tool can generate autotools inputs
build site has minimal requirements (vs. cmake which requires cmake, etc.)
cmake
many capabilities
butt-ugly
used by KDE; won over scons
boost.build
C++ only, its own scripting language (jam)
simple usage has pleasant, minimal syntax
seems to have good design
clear semantics/language concepts
scons
the simple glue approach
waf
a step backwards
redo
separate .do file for each target file, or a default.o.do
first line calls redo-ifchange DEPS...
can’t handle steps producing multiple target files
hash file contents; stored in .redo db
memoize.py, fabricate
just write a build script; tool will strace to determine dependencies (.deps)
can’t handle variations on the build
no parallelism
tup
like memoize/fabricate but uses LD_PRELOAD not strace
note-taking
wyneken
simple tool for writing notes in latex extended with bbcode tags
extracts tags, flags
article
website analytics
performancing metrics
basic (free) package has lots of overlap with google analytics
action-tracking (of individual users)
quantcast: not sure, but i recall stats aren’t so interesting
gnuplot
stacked bar charts:
set boxwidth 0.75
set datafile missing '-'
set style data histogram
set style fill solid
set style histogram rowstacked
plot 'data2' using 2:xtic(1), '' using 3

"hello there" 5 3
goodbye 2 9
wowza 4 4
must exit or set output ... or unset output to flush incomplete output files (eg pdfs may be incomplete)
use set term ... size SIZE to set the canvas size (see help canvas)
linetypes TODO
downloading media
google: intitle:“index of” [whatever you’re looking for]
web serving architecture
nginx: web server, caching reverse proxy, round-robin http load balancer
epoll-based fork of apache 1.3; supports fastcgi; modules for eg WSGI
as with any async IO system, good for many concurrent (low-activity) connections (eg comet)
easier to config than apache; stable; scalable; has features
used by lots of high-profile sites
filters: gzip, ranges, chunked responses, xslt, ssi, img resizing
parallel ssi
architecture: NBIO, AIO, …
master process: respawns workers, handles signals, notifies workers
worker: process conns
signals/socketpairs, shared mem
internals
graceful restarts/upgrades w zero downtime: fork & hand off sockets (ngx_init_cycle)
mongrel: ruby web server by zed shaw
god: monitors mongrel
unicorn: mongrel core + pull balancing (unlike load balancers)
all forked workers listen on same socket; leads to ‘thundering herd’ of all waking up, but a problem only when idle
gunicorn: Python greenlet unicorn
HAProxy: tcp/http load balancer; very configurable; can do smart load balancing; recommend in front of nginx
ssl, keep-alive are WIP
varnish: caching reverse proxy, http load balancer
faster than nginx?
uses (tons of) threads (and large addr space; designed for 64-bit)
already tuned by default
web serving advice
setups
ldirectord -> varnishes -> haproxy -> nginxes
nginx -> haproxy -> caching -> haproxy -> app
use ‘real’ web server like nginx in front of your app servers bc it’s better w slower conns, static files, more…
web serving case studies
github
originally: nginx -> haproxy -> mongrel threads/processes
now: nginx -> shared unix socket -> unicorn processes
message queues
starling: ruby mq; no durability
kestrel: starling in scala
AMQP: standard protocol for reliable MQs; requires intermediate brokers
1.0 supports 2PC
rabbitmq: reliable erlang AMQP; anecdotally unstable
activemq: not a serious AMQP impl, more of a sandbox
zeromq: fast; TODO http://nichol.as/zeromq-an-introduction
qpid: TODO
hedwig: reliable non-standard MQ; declarative, non-standard; used in pnuts; research focus on wide-area topology
python web serving
WSGI: either in-proc or out-of-proc
passenger: supports wsgi as well as ruby/rails
paste: elastic thread pool
tornado: fast but not fastest
fapws3: fastest wsgi, no HTTP/1.1
uwsgi: fastest wsgi; has nginx modules
gevent: fastest wsgi
flup: obsoletes fcgi; a threaded fastcgi-to-wsgi server
http://nichol.as/benchmark-of-python-web-servers
ldirectord: TCP load balancer; used by linux-ha
linux virtual server: lots of software
IPVS: tcp/udp load balancer
KTCPVS: app-level load balancing
linux-ha heartbeat: monitors & informs (on failure) 2-node clusters
heartbeat: clustering and communication layer
heartbeat 1: simple 2-node clustering system; manage resources running on either node
heartbeat 2: monolithic
heartbeat 3: just cluster membership; refactored cluster resource mgmt into pacemaker
backed by linbit; halted dev, maintenance mode now
pacemaker: cluster resource mgmt
engine is either heartbeat or corosync; corosync recommended
engine provides: reliable inter-node msging, consistent membership set and notifications
uses resource agents
LSB RAs: just LSB init scripts
OCF RAs: extensions of LSB RAs (eg: monitor, promote, much more)
legacy heartbeat RAs: same as LSB RAs but with odd status ops
openais whitetank: infrastructure + impl of SA Forum APIs (AIS standards)
openais wilson: SA Forum APIs only; refactored infrastructure into corosync
corosync: infrastructure refactored from openais, eg IPC/networking
pacemaker’s preferred engine
also manages GFS, OCFS2 partitions
backed by RH, novell
ipvs
cman: redhat’s clustering system; cman 1 sucked, entirely replaced by cman 2
cman’s distributed locking implemented in-kernel; fragile
clvm: shares single disk among multiple boxes
supported clustering systems: cman, openais, corosync, gulm
prone to deadlocks
keepalived: monitoring
refs
http://www.linuxvirtualserver.org/software/index.html
http://coolerq.livejournal.com/89739.html
http://clusterlabs.org/wiki/FAQ
javascript
tons of crap
objects aren’t maps: http://www.devthought.com/2012/01/18/an-object-is-not-a-hash/
can use Object.create(null) to create an “unpolluted” map
python
things i want in py3k
resumable pdb ctrl-c: http://bugs.python.org/issue7245
iterator versions of built-in functions/methods
cleaner syntax
exception tracebacks are traceback properties: http://www.python.org/dev/peps/pep-3109/
better concurrent.futures
stricter unicode/str distinguishing
greenlets
stackless spinoff
each switch = 2x memcpy, realloc, free (copying 20-100+K of mem)
eventlet, gevent built on this
doesn’t work w C extensions; relies on monkey-patching
can’t switch greenlets from C: event-loop/coroutine scheduler must be Python
violates amd64 abi
doesn’t correctly maintain PyThreadState per greenlet, so can eg drop exceptions
coev
uses ucontext.h; works w C extensions; GIL = incr/decr
can switch from C extensions
WSGI
based on CGI; agnostic as to whether persistent/fastcgi-like or CGI-like invocations
synchronous; not designed for event-based IO
uWSGI: looks janky?
uGreens: ucontext.h
mem mgmt
ref-counted + cycle GC; gc module triggers cycle
all objs (incl. ints) are heap objs; ints -1..99 are lazily interned
apache
MPMs
prefork: multi-process, single-threaded; default on *x
worker: multi-process, multi-threaded; added in apache2
.htaccess
reloaded on each access, which is bad for perf
generally not so bad for security
AllowOverride Options doesn’t actually enable everything
eg “Option SymLinksIfOwnerMatch not allowed here”
authentication types: basic (base64) and digest (MD5)
most advanced authentication backends plug into basic (pam, external)
basic should be used only behind SSL
external: uses any executable; use pwauth for crypt/pam
mod_authnz_external easier than mod_auth_pam, works with new apache arch, actively maintained, and doesn’t need apache to have read access to /etc/shadow
ruby
must require ‘rubygems’ first
ruby-talk better than #ruby

boost.serialization
binary format is quite verbose, has a lot of boilerplate overhead
serializing a single int takes 43 bytes; long long takes 47
still can’t wrap my head around the “stream error” exceptions it throws
tricky to use stringbuf, stringstream, etc. properly
issues: ios_base::binary? can’t work with stringstream(string(…))?
boost.test
you need to include boost/test/included/unit_test_framework.hpp
mime types
RFC 2045, RFC 2046, RFC 2047, RFC 4288, RFC 4289 and RFC 2049
rfc1524: A User Agent Configuration Mechanism For Multimedia Mail Format Information
xdg-utils, like xdg-open
luajit: tracing vm for lua
LJ1: faster than V8 by quite a bit, and smaller to boot
LJ2: approaches C speeds
freedesktop: formerly known as the x desktop group (XDG)
freebsd
kqueue/kevent: handles signals, FS (vnode) changes, AIO, monitoring processes
linux
dirty_ratio, dirty_background_ratio: control % RAM allowed to be dirty
more aggressive writing happens when crossed
pre-2.6.22: 10%/40% RAM thresholds
post-2.6.22: 5%/10% are defaults
threading
NTPL: modern threading system for Linux
LinuxThreads: separate processes per thread; doesn’t follow POSIX semantics in various ways (eg signals)
aio: TODO
epoll: TODO
futex: fast user-space mutex
single atomic instr (eg CAS)
kernel involved only on contention
TODO is this used anywhere?
top: shows 100% per core; procs exceeding this only show up as 99.9 as there’s only so much char space
use make V=1 to see the gcc commands (verbose)
interesting things in /proc:
PID/stack: stack trace
PID/pagemap: virtual to physical page mapping
PID/status: lots of interesting bits of info
interrupts: IRQ to CPU map
irq/NUMBER/smp_affinity: set individual IRQs
sys/kernel/core_pattern: where to output core dumps (or pipe to command)
sys/fs/binfmt_misc: register different ways to run different binary formats
PID/fdinfo/FD: includes pos in file
PID/maps: what files (usu. libs) the process has mapped, and where; useful for debugging env/LD_* issues
http://blog.ksplice.com/2011/01/solving-problems-with-proc/
ulimit can only lower not raise for non-root; root can raise; /etc/security/limits.conf can set global cap for non-roots
scheduler works on threads
huge pages
oldest/simplest huge-page interface: shmget() with SHM_HUGETLB and huge-page-aligned size; see hugetlbpage.txt
uses default Hugepagesize in /proc/meminfo (default_hugepagesz kernel param)
limited to shmmax sysctl param
HugeTLBFS: RAM-based FS; access w mmap/read; libhugetlbfs simplifies use
mount -t hugetlbfs none /mnt/hugetlbfs -o pagesize=64K,size=32G
anon mmap() w MAP_ANONYMOUS|MAP_HUGETLB
libhugetlbfs also provides get_hugepage_region()/get_huge_pages()
libhugetlbfs also auto-backs some region types when pre-linked/pre-loaded
if HUGETLB_SHM=yes then all shmget() requests
if HUGETLB_MORECORE=yes then all malloc (__morecore hook)
hugectl --heap ./target-application or hugectl --heap=64k ./target-application
text/data: must relink with -Wl --hugetlbfs-align
then hugectl --text --data --bss ./target-application
nothing for stack
tmpfs/ramfs
tmpfs: fixed size, may swap
ramfs: dynamic size (may have cap, or kernel will stall if OOM), no swap
syscalls
eclone: extended clone
madvise: how to handle paging
MADV_RANDOM, MADV_SEQUENTIAL, MADV_WILLNEED (expect access again soon), MADV_DONTNEED (expect no access; free up cache)
fadvise: how to handle file IO
FADV_RANDOM, FADV_SEQUENTIAL, FADV_WILLNEED, FADV_DONTNEED, FADV_NOREUSE (access data only once; no cache)
for reading, madvise to create a shared buffer w system file cache is preferable to having reading privately with O_DIRECT, but (blind) writes are faster w O_DIRECT (no need to read), and also more control over writeback (instead of playing w mlock/munlock) http://kerneltrap.org/node/7563
open
O_DIRECT: bypass cache; doesn’t imply O_SYNC
default stack size: 32K
linux kernel
kprobes: get runtime info of any kernel routine; used by systemtap
systemtap: kernel instrumentation with awk-like scripts (handlers attached to events, eg function return)
perfcounters: rich abstraction over hardware counters
perf: user program to work with perfcounters
kevent/eventfs: WIP; merges socket io, network AIO, poll, inotify, timers
FSs: ext?, btrfs, logfs, ceph
kvm: great linux success story; uses qemu
cpuidle: governs when system enters which C states
set timer interrupt frequency
CONFIG_HZ_100=y
CONFIG_HZ=100
connector kernel module is useful for process monitoring; when procs die, kernel notifies you; you can restart, recover, etc.
CONFIG_CONNECTOR=y
CONFIG_PROC_EVENTS=y
intel xeons can offload network data coying to DMA engine with intel I/OAT DMA engine; see also DCA
linkage
if a library contains an _init symbol or function marked with attribute((constructor)), it will be immediately when loaded. There’s no way to “pause” the library loading, switch seccomp on, and then allow the library loading to continue. (http://stackoverflow.com/questions/1019707/sandboxing-in-linux)
type coercion: implicit type conversion
dwarf: the debug info format used by gcc/gdb/etc
sipb-mp3
zephyr
You have to mkdir ~/zlog first; barnowl will not create the directory for you.
.anyone is your buddy list.
.zephyr.subs is your class/instance subscription list.
znol tells you who on your buddy list is online
if this gives you “znol: No such file or directory while getting WindowGram port” then try zwgc -ttymode
zwrite lets you send messages
when starting to send a message you’ll get a ping from that person (this doesn’t seem to show up in barnowl by default)
proxies
HTTP: for web traffic; analyzes headers to determine dst; eg squid
SOCKS: general TCP/UDP proxy
ssh
tun-based VPN: https://help.ubuntu.com/community/SSH_VPN
ProxyCommand: the command to use to connect to the remote sshd (instead of plain ssh)
example given in man page is nc to connect via an http proxy
see also corkscrew which enables connecting via http proxy
not yet sure what corkscrew’s value-add is
shell scripting
pidof: Linux-specific; use pgrep instead
amazon ec2
ec2-api-tools
ec2-describe-images -o self -o amazon: list AMIs owned by me or amazon
ec2-add-keypair <pairname>: create a keypair named <pairname> (amazon registers this); stored the key into a file so that you can use it from ssh
ec2-run-instances <ami> -k <pairname> -t m1.large: start a large instance using the AMI <ami> and the keypair <pairname>
ec2-describe-instances <inst>: show status
ec2-authorize default -p 22: open port 22
ec2-get-console-output <inst>
ec2-ami-tools
scp -i ... <pk> <cert> <inst>:/mnt
ec2-bundle-vol -d /mnt -k /mnt/<pk> -c /mnt/<cert> -u <acct> -r [i386|x86_64] -p sampleimage: run on remote host; outputs to /mnt/
ec2-upload-bundle -b <bucket> -m /mnt/<manifest> -a <access> -s <secret>
ec2-register <bucket>/<manifest>: returns AMI ID
ec2-register <ami>: returns AMI ID
ec2-register -a x86_64 -n $NAME \
    -b /dev/sda1=snap-$SNAP::false \
    -b /dev/sdb=ephemeral0 \
    -b /dev/sdc=ephemeral1 \
    -b /dev/sdf=snap-$SNAP::false \
    --kernel aki-$KERNEL --root-device-name /dev/sda1
rds
multi-az replication: synchronous to inaccessible failover; probably DRBD
read replicas: async replication; probably built-in mysql replication
ebs
max 100 EBS vols, max 20TB
no device write caching (see hdparm -W, barrier=1); all fsync’s are durable
elb
max 5 ELBs
requires you to point to it via CNAME, hence root domains must eg http-redirect to a www subdomain that CNAMEs to it; that’s a SPOF
only provides DNS; can’t map an (elastic) IP to an ELB
instances
each inst has 2 interfaces, public and internal
max 20 running; max overall = 4 * max running
can lock inst to prevent termination via mgmt console or api, only via cmd line
elastic IPs
takes minutes for assignment to propagate
http://developer.amazonwebservices.com/connect/thread.jspa?messageID=85617&#85617
http://developer.amazonwebservices.com/connect/thread.jspa?messageID=173789&#173789
http://developer.amazonwebservices.com/connect/entry.jspa?externalID=1346&categoryID=112
only 1:1 public/elastic:private NATings
max 5 without appeal; 01hforunmapped; .01/GB for mapped; $.10/remap if >100 remaps that month
reverse DNS can be applied for
cloudwatch
~$10/mo/inst
monitors cpu, disk, network
autoscale
launch configuration: img, inst type
auto scaling group: launch config, AZs, min size, max size
trigger: measure (cpu), stat (avg), period, lo/hi thresh & scale increments
ami’s
backed by ebs or s3; public/private/shared (ACL)/paid
ebs: <=1TB, <1m boot, can stop, costs more to run
can change kernel/ramdisk/inst-type/data when stopped
single command/call to create
snapshots stored in s3, confusingly; new ebs vol created from root snapshot on boot
by default, vols that ami boots with have DeleteOnTermination=true
stopping loses elastic ip
s3 aka instance store: <=10GB, <5m boot, can’t “stop”, costs less to run
can’t change kernel/ramdisk; no ‘stopping’
requires ami tools to create; involes “bundling”
creation/registration are separate steps
ec2-create-image: halt, snapshot all vols, create/reg the ami, reboot
kernels (aki’s): only amazon/selected vendors can create
user selectable kernels useful for keeping inst up to date w security fixes & updates
svn
svn:externals: foreign repo (fixed commit or HEAD-tracking) linked to from a dedicated subdir
vendor branches: usu. in /vendors/; a way to track external software
import files from vendor releases to its own dir, tagging the versions
then merge diff btwn versions into current project trunk: svn merge https://repo/vendors/foo/1.{0,1}
svn log -rHEAD:1 ...: really show all log entries; default range is BASE:1, which means since last svn update
svn cat -r 1 https://project.com/file.txt@HEAD: cat the file that is currently named /file.txt, but as of revision 1 (regardless of whatever name it had back then). 1 is the operative revision, and HEAD is the peg revision.
BASE: the revision of the working copy (local copy without modification); cannot be used on URLs
COMMITTED: the last revision in which the given item changed; cannot be used on URLs
PREV: COMMITTED - 1
fences/barriers
mb()/barrier() in Linux kernel
fonts
WOFF: repackaging of old sfnt/TrueType/OpenType format
gzipped TTF file + opt’l sections for XML and ‘private data’
no need for WOFF to exist
TrueType has tables; opt’l sections could’ve been tables
HTTP already uses gzip
but having this was how the industry was able to get type foundries and MS on board
didn’t want ppl using, even though trivial to convert to TTF
http://www.imperialviolet.org/2010/05/03/woff.html
css
designed for horizontal layout/flow layout, not vertical
specificity: id = 100, class = 10, else = 1; add up
@ rules: import, media, charset, font-face (custom fonts), page
http://www.htmldog.com/guides/cssadvanced/atrules/
cross-browser gradient
filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#cccccc', endColorstr='#000000'); /* for IE */
background: -webkit-gradient(linear, left top, left bottom, from(#ccc), to(#000)); /* for webkit browsers */
background: -moz-linear-gradient(top,  #ccc,  #000); /* for firefox 3.6+ */
css3
border-radius, border-image, box-shadow/text-shadow, rgba()/opacity, @font-face, attribute selectors, sibling combinator, pseudoclasses, columns, multiple backgrounds, word-wrap, box-sizing, media queries
http://www.smashingmagazine.com/2009/01/08/push-your-web-design-into-the-future-with-css3/
http://www.smashingmagazine.com/2009/06/15/take-your-design-to-the-next-level-with-css3/
http://sixrevisions.com/css/css3-techniques-you-should-know/
box-sizing: border-box (or content-box)
word-wrap: break-word (or normal)
columns: column-count, column-gap, column-rule, column-span, …
media queries:
@media all and (max-width:480px) {...}: view narrower than 480px; no longer need JS adaptive fluid layout
@media screen and (color) {...}: color screens
combinators
descendant: h1 em; div * p (p must be grandchild or further)
child: body > p
adjacent sibling: math + p
general sibling: div ~ img
attribute selectors: a[title$="tweetCC"] {...}
border-image: url(/images/border-image.png) 5 repeat
box-shadow: 2px 2px 10px #ccc (xoff yoff blur color)
text-shadow: rgba(...) 0 1px 0 (color xoff yoff blur)
rgba(100, 100, 155, 0.2)
@font-face example
@font-face {
  font-family: 'Anivers';
  src: url('/images/Anivers.otf') format('opentype');
}
horizontal align: make margin-left, margin-right auto
make elt screen-height: height: 100%
height, line-height, top/bottom margins, width (default 100%) can be set only for block elts
min-width, min-height
display: none takes up no space; visibility: hidden still takes space
pseudoclasses
use single colons (:)
a: link, visited, hover, active
p:first-child: the first p instance (ignore child)
p > i:first-child: the first i in any p
q:lang(no): a norwegion quote (<q lang="no">)
css3: nth-child(n), nth-last-child(n), last-child, checked, empty, not(s)
generated content: counter-reset, counter-increment, counter(), counters() for nested counters (1.1.1)
body { counter-reset: chapter; }
h1:before {
  content: "Chapter " counter(chapter) ". ";
  counter-increment: chapter;
}
h1 { counter-reset: section; } ...
pseudoelements
use double colons (::)
first-line, first-letter
before, after: insert something before/after, eg .x:before { content: "prefix: " } or .x::after { content: url(...png); }
css3: ::selection
::-webkit-scrollbar-thumb
CSS expressions: IE-only reactive property-setting
inheritance: eg color is inherited (setting on body will color everything)
can’t vertical-align block elements like divs, floats
vertical-align only applies to inline elts & table cells (diff meanings)
until css3 advanced layout: http://www.css3.info/advanced-layout-module-gets-a-refresh/
workarounds:
http://blog.themeforest.net/tutorials/vertical-centering-with-css/
http://phrogz.net/CSS/vertical-align/index.html
inline-block & line-height
http://csscreator.com/node/30624
http://archivist.incutio.com/viewlist/css-discuss/86031
line-height: height of line box (note: can’t be of inline box)
values: normal, inherit, %, float, and absolute unit measure
% and float are ratios of the font size
%: inheritance propagates calculated value, not orig %
float: inheritance propagates orig float, not calculated value
box model: blocks contain lines contain inlines
containing/block-level boxes: top-level
line boxes: height is that of tallest contained inline box
inline boxes: eg inline elts; everything else are anonymous inline boxes
content area aka em-boxes: tightly surrounds the text; set by font-size
leading: line-height - font-size
content area is middle-aligned in (in)line box
content area can be smaller than actual font size if constrained by containing line-height
http://www.w3.org/TR/css3-box/
http://www.slideshare.net/maxdesign/line-height
http://meyerweb.com/eric/css/inline-format.html
pre-IE6 quirks mode aka box model hack: border/padding included in width/height; unfortunately, this is arguably more intuitive, hence reintroduced in box-sizing
border-box: tells browser to behave like IE6
position:
static: the default
absolute
relative makes all children that have position: absolute actually framed in this elt’s coords (origin)
fixed fixes something in place regardless of scroll pos
background images: background: url(widget-image.gif) no-repeat
wildcards: * { margin: 0; padding: 0; }
nice header style:
h1 {
  font-family: arial; /* or sans-serif */
  font-weight: normal;
  letter-spacing: -3px;
  text-transform: uppercase;
}
posix calls
difftime
windows usage
networking
lmhosts, hosts, etc. all in 32
ipconfig /flushdns
windows usage
shutdown -a: abort shutdown
windows steadystate aka pc safeguard: sandboxing system for a whole user
useful for trying out software
TODO how secure is it?
group policy: control reg, NTFS, audit/security policy, installs, logons/logoffs
eg block taskmgr, some fs access, downloading exe’s
group policy object (GPO): periodically pulled by clients from active directory
offline files: caching of remote files; many changes in vista
on reconnect, handles to open files are auto redirected to remote vers
efficient bitmap differential transfer protocol
distributed file system (DFS): merge many SMB shares; in win server
folder redirection: a user GPO; can specify %USERNAME%; useful for network roaming; similar to mount --bind
roaming profile: requires domain controller
AppData\LocalLow: low-integrity apps like IE addons
windows app dev
wow64
FS redir: %windir%/system32 reserved for 64-bit bins, so redir to %windir% to %windir/syswow64
disable on per-thread basis
%windir%/sysnative for raw access (doesn’t exist outside 32-bit apps)
security zone tainting
foo.ps1:Zone.Identifier
resources
FindResource, LoadResource
compile resource script .rc with rc.exe and link in resulting .res
strings
converting: http://msdn.microsoft.com/en-us/library/ms235631(VS.80).aspx
linking
std libs
http://support.microsoft.com/kb/154753
http://msdn.microsoft.com/en-us/library/abx4dbyh(VS.80).aspx
/MT for static, /MD for shared (DLL), /M[TD]d for debug
/LD for generating DLLs
.libs: archives of .obj’s
import libs are thin wrappers for shared libs
can specify #pragma comment(lib, "PocoXXXmt.lib) to automatically pull in libs
DLLs
DisableThreadLibraryCalls(): per-DLL optimization
otherwise windows calls into DLL on ea thread create/destroy
useful for DLLs that maintain per-thread state, eg CRT or winsock
http://blogs.msdn.com/larryosterman/archive/2004/06/03/147736.aspx
VirtualProtect: changes protection on region of committed pages in VA space
modes: x, rx, rwx, rx+cow, none, r, rw, cow
conditional options: guard, uncached, write-combined (for devices?)
UNC paths: C:, \host, and \?:or \?
command line
compiler: cl
just produce obj: cl /c app.cpp
/Zl: no C runtime (CRT) library (eg libcmt.lib, libcpmtd.lib, etc.)
linker: link
eg link app.obj ws2_32.lib
if no CRT, must specify WinMain-like function with /entry:
CRT vs Windows API
main vs WinMain
compiler intrinsics: eg abs, memset are compiler-produced with pragma
`#pragma intrinsic(abs, memset)
dumpbin: useful disassembler and pecoff parser
dll search order: depends on whether safe dll search mode is enabled
enabled: $(dirname $0):system32:windows:.:PATH
disabled: $(dirname $0):.:system32:windows:PATH
unicode
TEXT/_T, UNICODE: for windows functions
_TEXT, _UNICODE: for CRT functions (_tcslen)
MultiByteToWideChar (ANSI/UTF-8 to UTF-16) and WideCharToMultiByte
? is used for non-translatable chars
http://msdn.microsoft.com/en-us/library/cc500316.aspx
atomics
functions Interlocked*: eg InterlockedIncrement64
cross-platform
mingw: set of headers written to be used by gnu toolchain to produce windows binaries
for use on either posix or windows
packages
mingw-runtime: default CRT (msvcrt) headers and import libs
w32api: windows api headers and import libs
windows-native apps: gcc, gdb, make, binutils, misc utils
msys: windows-native sh
portabel executable (PE) format
based on unix COFF
.NET exe’s extend PE’s
have just one instance of program running at a time by trying to create a named Monitor either under Local or Global
can have 0-thread procs
processes/threads
priority is inherited iff parent is < normal class (below normal/idle)
groups: used by GenerateConsoleCtrlEvent (kinda like posix signals)
default stacks on 32-, 64-bit: commit 4KB, reserve 1MB
stack guard pages trigger commits on reserved stack space
limited by virt mem on 32-bit OS, commit on 64-bit OS
200 KB min working set size (code + data)
NUMA support: processor groups
threads assigned to groups; processes can straddle n groups
Windows on Windows 64 (WOW64): runs 32-bit app on 64-bit OS
64-bit process executes 64-bit code on behalf of 32-bit threads
threads starts in 64-bit mode
stack
StackWalk64: obtains a stack trace
windows kernel dev
SKU: TODO
data execution prevention (DEP): TODO
memory
virt mem: reserved or committed, private or pagefile-backed (shared)
commit charge: system-wide total commit
commit limit: approx. phys + swap; swap can grow
PF-backed must be allocated by opening handle to a Section object
only private can be accounted to processes
upper 1GB or 2GB of 4GB virt addr space is kernel
memory manager
PFN database: array of 28-byte structs, one per page; tracks assignments to addr spaces
devices are mapped into phys and virt addr space; eg gfx card can take up a lot on 32-bit systems
crash dumps
mini (64K), kernel (XP produces ~60M on 256M), full
system service dispatches (syscalls)
all go through ntdll.dll; Zw* for kernel, Nt* for user
many undocumented
trap into KiDispatchService/KiSystemService
ZwCreateFile:
  mov eax, SERVICEINDEX
  lea edx, [esp+4]
  int 2eh # sysenter on xp
  ret 2ch
KeServiceDescriptorTable[Shadow]: global syscall tables
KSDT is exported by ntoskrnl.exe, but not KSDTS
syscall nums are OS version-dependent
DWORD * addr = (DWORD *)
  (1+(DWORD)GetProcAddress(GetModuleHandle("ntdll.dll"),
                           "NtCreateSection"));
kernel finds target addr in thread environment block (TEB)
TEB contains regs, pri, process ptr, service descriptor table (SDT) ptr, more
ie, there’s a per-thread syscall table
SDT contains ntoskrnl and win32k system service tables (SSTs)
SST contains
system service dispatch table (SSDT): array of fn ptrs
always-0 field
number of entries in SSDT/SSPT
system service parameter table (SSPT): array of byte counts
for win32k syscall numbers, subtract 0x1000 to get offset
bits 12, 13 specify which of the four possible SDTs to look in
SDT ptr offset in TEB is also OS version-dependent
struct SYS_SERVICE_TABLE {
  void **ServiceTable;
  unsigned long CounterTable;
  unsigned long ServiceLimit;
  void **ArgumentsTable;
};
when changing, either disable write protection in cr0, or map the SSDT’s phys addr into a new virt addr and access via that
refs
“rootkit arsenal: escape and evasion”
russinovich. nt syscall hooking
http://www.codeproject.com/KB/system/soviet_protector.aspx
http://www.volynkin.com/sdts.htm
http://www.osronline.com/showthread.cfm?link=93915
http://forum.sysinternals.com/forum_posts.asp?TID=3758
http://unlmtd.wordpress.com/2007/07/29/hooking-nt-functions/
hooks
user level injection
SetWindowsHookEx: only certain uninteresting hooks
window msgs, debugging, foreground thread going idle, kbd/mouse, shell
browser helper object (BHO): COM DLL for IE hookig
Office add-ins
CreateRemoteThread(targetProc, ..., &LoadLibrary, "C:\\my.dll"): only works if kernel32.dll!LoadLibrary is always mapped to same loc
reqs monitoring running procs; call CreateProcess yourself or use PsSetCreateProcessNotifyRoutine
kernel level interception
NT syscall hooking
PsSetCreateProcessNotifyRoutine
user level interception
proxy DLLs: share same name and exports as established DLL
easy with function forwarders: #pragma comment(linker, "/expo.rt:DoSomething=DllImpl.ActuallyDoSomething")
binary rewriting
detours: injects trampolines in-mem
debugger spying
import address table (IAT): change addr in .idata in PE
refs
Protecting by hooking ZwOpenProcess
Detecting Windows NT/2K process execution
Hooking Nt* functions
debugging
virtual pc: use the virtual com port
run this on the target machine in an admin cmd; bcdedit is a program that edits boot settings, which in older systems was specified in a file boot.ini:
bcdedit -debug on
bcdedit -dbgsettings serial debugport:1 baudrate:115200
in the vm settings, configure the com port to map to named pipe \\.\pipe\<pipe name>
reboot the target
prepare the environment variables for windbg to be able to automatically fetch symbols and sources (not sure what the syntax here means but \\symbols\symbols is a microsoft-internal symbol server)
set _NT_SOURCE_PATH=SRV*;
set _NT_SYMBOL_PATH=srv*c:\syms*\\symbols\symbols
connect to vm on a remote machine (same as args to kd; if local machine use . for vpc_host_machine):
windbg -k com:port=\\<vpc_host_machine>\pipe\<pipe name>,pipe,resets=0,reconnect
resets=0: required for VPC; use 2 for a competing VM product
reconnect: does 2 things
if named pipe is not found on target, wait for it
if pipe disconnects, wait for reconnect
windbg: gui
used to be for user mode only while kd for kernel; now all unified into windbg
hit ctrl-break to break the target
commands/examples
bp nt!NtReadFile: set a breakpoint in driver NtReadFile of driver nt
g: continue
??: evaluate a (C++) expression
ntsd: cui
kd: doesn’t exist anymore; used to be kernel debugger
KeBreakinBreakpoint
KD_DEBUGGER_ENABLED: whether system was booted with /debug
see also: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control:SystemStartOptions
KD_DEBUGGER_NOT_PRESENT: whether debugger is attached
debug printing
DbgPrint/KdPrint: same as DbgPrintEx(component=DPFLTR_DEFAULT_ID, level=DPFLTR_INFO_LEVEL, ...)
get a component mask (dword) via windbg: dd KD_DEFAULT_MASK (default 0)
set the level to accept info: ed KD_DEFAULT_MASK 8
http://www.adp-gmbh.ch/win/misc/writing_devicedriver.html
development models
windows driver model (WDM)
windows driver foundation (WDF)
kernel mode driver foundation (KMDF)
user mode driver foundation (UMDF)
required functions
DriverEntry
AddDevice
Dispatch* (eg DispatchCreate)
Unload
build
CONTAINING_RECORD(addr, type, field): contains the addr, type, and field
useful for LIST_ENTRY
LIST_ENTRY: an entry in a doubly linked list
use this by including it into your struct, e.g.: LIST_ENTRY
use CONTAINING_RECORD to get the containing struct given a list entry
lookaside lists: adaptive memory pool for allocating/freeing fixed size blocks
if can’t alloc from/free into the pool, go through normal heap mgr
pool grows/shrinks adaptively
communicating with user mode: IRP, local sockets, ETW providers
oacr/prefast
experience:
Misuse of length parameter in call to ‘strncat’. Pass the number of remaining characters, not the buffer size of ‘metaLabel’.
threads/processes
thread: unit of scheduling; has regs, IP, SP
process: >=1 thread; has handles, sec token, AS
threads
each has 2 stacks: 1 user, 1 kernel (1:1 scheduling)
priorities
1-15: dynamic priorities; kernel can boost/drop this temporarily to increase system tput (but never boosted to real-time)
boosted when it completes an IO req, waits for event/semaphore, or starves; also, some situations when threads in GUI or foreground
drivers can specify boost in IoCompleteRequest and KeSetEvent, KePulseEvent, KeReleaseSemaphore
16-31: real-time priorities; never changes
base priority: inherited from process’s base priority
current priority: priority at any given time
kernel-mode driver running in context of user thread: base pri = pri of user process
contexts
permissions: eg driver routine called from user mode context can call Zw* routine in kernel context
for driver routines, context depends on device type, driver position in device stack, and other activities in system
eg dispatch routines run in context of requesting thread, but might queue IO op for processing by lower level drivers
some routines run in system thread context; have addr space of system process and security rights of OS itself
instead of calling PsCreateSystemThread, borrow a system thread from pool shared by all drivers
IoQueueWorkItem(...IO_WORKITEM...)
3 queues: delayed, critical, hypercritical
run at IRQL_PASSIVE_LEVEL
using up too much time or too many workers can deadlock the system
refs: http://msdn.microsoft.com/en-us/library/ms810029.aspx
interrupt request level (IRQL): distinct from x86 IRQLs
low to high: passive, apc, dispatch, cmc, device interrupt (dirql), pc profile, synch, clock, clock2, IPI, power, high
all threads run at IRQL < DISPATCH_LEVEL
thread scheduler itself (“dispatcher”) runs at DISPATCH_LEVEL
hence any thread running at/above DISPATCH_LEVEL has exclusive use of cpu
DISPATCH_LEVEL interrupts are masked off on the cpu, so thread scheduler is disabled / cannot schedule any other thread
using this you can run uninterrupted but cannot incur page faults (bc doing so requires suspending thread until swapped)
ISRs, DPCs run here
for shared memory synchronization, drivers should use a lock that raises the IRQL to the highest level at which any code that accesses the data can run
eg use spin lock to protect data that can be accessed at IRQL=DISPATCH_LEVEL
cpu-specific IRQLs
DISPATCH_LEVEL
DIRQL
HIGHEST_LEVEL
thread-specific IRQLs
PASSIVE_LEVEL: normal operating state; use thread scheduling pris
all user code; only kernel can elevate
threads are both preemptible and interruptible
PASSIVE_LEVEL in critical region:
use KeEnterCriticalRegion/KeLeaveCriticalRegion
APC_LEVEL: most commonly associated with paging IO
only IRQL < DISPATCH_LEVEL can use paged pool
refs: http://msdn.microsoft.com/en-us/library/ms810029.aspx
asynchronous procedure calls (APCs)
software interrupts targeted at a specific thread
system uses APCs to perform work in context of a particular thread, eg writing back the status of an IO operation to the requesting app
how a target thread responds to APCs depends on the thread’s state and the type of APC
driver installation
INFs: jcondit says these are evil and that osrloader is much nicer to deal with
http://msdn.microsoft.com/en-us/library/ms878125.aspx
http://ext2fsd.sourceforge.net/documents/irql.htm
devices
have a number of entry point routines that a driver can set
a driver can own many devices; usu. create one device obj for each device
device extensions: main storage space for device state
objects & object manager
objs are ref-counted
a process can own 16M handles
kernel objects: accessible only to rest of kernel
eg physical devices, certain synchronization services
executive objects: accessible to all; apps can wrap objects too
conf mgr: reg key
executive: sem, mutex, event
IO mgr: file
proc mgr: process, thread
mem mgr: section
object types
proc, thread, job, file, file mapping, access token, event, sem/mutex, timer, key, desktop, clipboard, windowstation, symbolic link
objects: header (used by obj mgr) and body
named and unnamed
user-accessible named objects are under \BaseNamedObjects
by default, creating an object creates it under Local\ which maps to global Session\n\BaseNamedObjects
if want to reference global BaseNamedObjects\xxx use Global\xxx
DeviceIoControl: IOCTL, i.e. direct user-to-kernel-driver interface
http://msdn.microsoft.com/en-us/library/aa363147(VS.85).aspx
ObReferenceObjectByHandle: get ptr to an object given a handle to it
increments ref count
TODO file names: “\?”, “Global??”
http://msdn.microsoft.com/en-us/library/aa365247(VS.85).aspx
handles: managed in per-process tables
handle entry: pointer + 32 permission bits (8 B on 32-bit, aligned 16 B on 64-bit)
3 levels of tables; upper indirect table is just pointers to lower tables
tables allocated from paged pool
DBG macro
ASSERT, ASSERTMSG; NT_ASSERT: http://winprogger.com/?p=1156
overlapped IO: TODO
IO/IRPs
IoCallDriver transfers IRP ownership down
IoCompleteRequest transfers IRP ownership up
IoMarkIrpPending holds the request (for async processing)
Unless an IRP’s dispatch routine completes the IRP (by calling IoCompleteRequest) or passes the IRP on to lower drivers, it must call IoMarkIrpPending with the IRP. Otherwise, the I/O Manager attempts to complete the IRP as soon as the dispatch routine returns control. "
IO mgr creates an array of IO stack locations for each IRP
each driver owns one IO stack location; get with IoGetCurrentIrpStackLocation
each driver responsible for calling IoGetNextIrpStackLocation to set up next-lower driver’s IO stack location (if not passing new IRP altogether)
IoAllocateIrp creates a new IRP
eg used by driver to break IRP up into sub-IRPs (maybe to multiple other drivers)
alternative: reuse IRP by setting up next driver’s IO stack location
completion routine
lowest level driver sets result status on IRP
on device interrupt, interrupt service routine (ISR) does min work:
stops device from interrupting
saves necessary context about operation
calls IoRequestDpc to queue a deferred procedure call (DPC) that completes the requested op at a lower hw pri
DPC
calls IoStartNextPacket to start processing of next packet
sets IRP status and calls IoCompleteRequest
ref: http://msdn.microsoft.com/en-us/library/ms795764.aspx
ref: http://msdn.microsoft.com/en-us/library/ms795773.aspx
symbols
public symbols: basic syms like functions and globals; needed for even rudimentary debugging
private symbols: data structures, locals, line numbers
full symbol file: contains both public and private; confidential
stripped symbol file: contains public only; avail on internet symbol server
user-kernel comm
events: simplest
best approach: named event, created by user
passing names vs passing handles: handles are per-proc
user- vs kernel-created: IoCreateNotificationEvent uses security attrib of current thread, so if created from system thread, then need to be Admin user process to access
passing data: TODO
inverted call: uses pended IRPs
refs
event WDK sample
Sharing is Caring
Calling User Mode—Using the Inverted Call Model
wfp callouts
arbitration
if FWPS_RIGHT_ACTION_WRITE set in classifyOut->rights, then it’s soft permit/block; allow override
else, it’s hard permit/block; overrides count as vetoes (audit logged)
callout types
modify vs inspect: block orig then reinject; callouts are TERMINATING
out-of-band vs inline: pend/block-with-absorb orig then reinject
pnp: characteristic of bus/device spec that facilitates auto discovery
industry standard architecture (ISA): early PC bus standard; superseded by PCI
applies across various buses: ISA, PCI, USB, PCMCIA, etc.
upnp: totally different; for networks
driver memory management
dynamic address space layout
http://www.nynaeve.net/?p=261
paged vs non-paged pool, system cache size, etc.
regions used to be fixed at boot
resident available mem: phys mem for kernel stacks, nonpaged pool, certain kernel code/data
paged/non-paged pools
pools: the kernel’s heap; eg nonpaged pool is not all non-pageable code/data
control pageability via compiler pragmas (text/data sections only) or via runtime functions
specify to heap allocator
the more pageable, the better
nonpaged
procs, threads, mutexes, sems, events, files, IRPs
limited by phys mem
takes from resident available mem
paged
registry, sections, handle tables
limited by addr space, commit size
memory descriptor list (MDL): maps VM to PM; opaque struct
PTEs for VM->PM; MDL for buffers associated with DMAs
for pageable mem: correspondence is temporary
IRPs can be associated with MDLs
MDL has pointer to IRP
IRP has pointer to first MDL in a chain of zero or more MDLs (MDLs linked by next pointers)
memory registers
drivers have full access to virt addr space, but not devices
devices need to use HAL map registers
globals, device extensions are in non-paged pool
MmMapIoSpace: map phys addrs (ie bus-relative IO addrs) to virt addrs
ndis
net buffer lists can be chained together
net buffer lists can net buffers chained together
NBL has pointer to head NB
NB has pointer to next NB
NB has MDL chain of packet buffers
synchronization
can wait on event, mutex, sem, thread, timer
some of these have diff meetings; eg thread means thread terminated
events have state (set or unset)
timing
KeDelayExecutionThread: sleep current thread
TerminateThread can still kill, even if alert=0
KeQueryPerformanceCounter: finest grained running count
“KeQueryPerformanceCounter is intended for time-stamping packets or for computing performance and capacity measurements. It is not intended for measuring elapsed time, for computing stalls or waits, or for iterations.”
“Use this routine as infrequently as possible. Depending on the platform, KeQueryPerformanceCounter can disable system-wide interrupts for a minimal interval. Consequently, calling this routine frequently, such as in an iteration, defeats its purpose of returning very fine-grained, running time-stamp information. Calling this routine too frequently can degrade I/O performance for the calling driver and for the system as a whole.”
KeQuerySystemTime: returns count of 100-nanosecond intervals since January 1, 1601; updated approx every 10 ms; computed for GMT
ExSystemTimeToLocalTime
KeQueryTickCount: returns a count of the interval timer interrupts that have occurred since the system was booted
KeQueryTimeIncrement: returns the number of 100-nanosecond units that are added to the system time each time the interval clock interrupts
sharing mem btwn user and kern
METHOD_BUFFERED: all in/out data copied to temp intermediate buffer; safest but slowest
METHOD_{IN,OUT}_DIRECT: in is buffered access; out has MDL; more details
METHOD_NEITHER: direct access to user memory
need to use MmProbeAndLockPages
languages
c++ is totally usable in kernel! but not stl
useful windows cmd commands
where
tlist
why google presentations sucks
can’t change master slide formatting
no anti-aliasing
no keyboard friendliness
can’t group objects together
ugly default themes
no image editing capabilities, e.g. cropping
rudimentary drawing tools
no animations
no paste special
stability issues: fatal errors
