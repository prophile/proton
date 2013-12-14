#Proton (1.0.0-rc1 [SemVer](http://semver.org/))

Proton defines a protocol for Student Robotics match scoring scripts to
interface with other services.

##Execution

1. A proton compliant program MUST be either a script including a `#!` line
   or be a binary.

2. A proton compliant program MUST be executable via a command line or by
   shelling out to. This typically means that it has the executable bit set.

##Inputs

1. A proton compliant program MUST consume a single argument which is the
   path to a YAML file containing a computerised interpretation of a Student
   Robotics scoresheet.

    1.1 A proton compliant program MUST expect 4 keys at the top level of the
        YAML file. Each key corresponds to a TLA of a team that participated in the
        match. A YAML file without 4 top level TLAs is malformed.

    1.2 A proton compliant program MUST NOT score any team who's TLA entry in
        the file has the key value pair `present:false`.
        A `present` key with a non-boolean value is malformed.

    1.3 A proton compliant program MUST NOT score any team who's TLA entry in
        the file has the key value pair `disqualified:true`.
        A `disqualified` key with a non-boolean value is malformed.

    1.4 A proton compliant program MUST exit with 1 if the input is malformed
        YAML or does not comply with either rules 1.1, 1.2 or 1.3

2. A proton compliant program MUST NOT block on any input from stdin.

##Outputs

1. A proton compliant program MUST print a YAML dictionary to STDOUT if it
   succeeds.

    1.1 The YAML output MUST contain exactly top level keys as the provided
        input file with one of:
        * A numeric value of the team was present
        * The string "DNS" if the team was not present in the match.
        * The string "DSQ" if the team was disqualified from the match.

2. A proton compliant program MUST exit with 0 if it succeeds.

3. A proton compliant program MAY refuse to process an input if it detects
   nested scoring values it is unable to process. If this occurs it MUST
   exit with 2.

4. A proton compliant program MUST NOT print anything to STDOUT if it fails.

5. A proton compliant program MAY print to STDERR under any circumstances.


##Examples

###Valid inputs

```yaml
CLF:
 squares : [[1,2,1],[1,0,1],[0,0,0]]
PSC:
 squares : [[0,0,0],[0,2,0],[0,p,0]]
BGR:
 squares : [[0,0,0],[3,0,0],[0,0,0]]
QEH1:
 squares : [[0,0,0],[6,0,0],[0,0,0]]
```

```yaml
CLF:
 present : false
 squares : [[1,2,1],[1,0,1],[0,0,0]]
PSC:
 squares : [[0,0,0],[0,2,0],[0,p,0]]
BGR:
 squares : [[0,0,0],[3,0,0],[0,0,0]]
QEH:
 squares : [[0,0,0],[6,0,0],[0,0,0]]
```

###Invalid inputs at the protocol level

```yaml
1:
 present : false
 squares : [[1,2,1],[1,0,1],[0,0,0]]
PSC:
 squares : [[0,0,0],[0,2,0],[0,p,0]]
BGR:
 squares : [[0,0,0],[3,0,0],[0,0,0]]
QEH:
 squares : [[0,0,0],[6,0,0],[0,0,0]]
```

```yaml
CLF:
 present : 1.0
 squares : [[1,2,1],[1,0,1],[0,0,0]]
PSC:
 squares : [[0,0,0],[0,2,0],[0,p,0]]
BGR:
 squares : [[0,0,0],[3,0,0],[0,0,0]]
QEH:
 squares : [[0,0,0],[6,0,0],[0,0,0]]
```

###Valid responses

```
CLF: 41.0
PSC: 12.0
BGR: 7.0
QEH: 18.0
#with exit code 0
```

```
#with exit code 1
```

```
#with exit code 2
```

###Invalid responses

```
CLF: 41.0
PSC: 12.0
QEH: 18.0
#with exit code 0
```

```
#with exit code 0
```

```
CLF: 41.0
PSC: 12.0
BGR: 7.0
QEH: 18.0
#with exit code 1
```
