# Edit Decision List (EDL) support

The CMX3600 Edit Decision List (EDL) format supports custom extensions through the definition of dedicated directives following the edit statements in the decision list. In order to support AMF linkage through EDL, the following directives are defined:

1. AMF_UUID

2. AMF_NAME

These two directives enable the linkage of AMF files, independently for every clip listed in the EDL file. Both AMF_UUID and AMF_NAME are defined as optional. The linkage rules are described below.

## AMF_UUID

The AMF_UUID column shall be used to convey the AMF UUID from the amfInfo/uuid element. The format of the column entries must use the canonical textual representation, the 16 octets of a UUID are represented as 32 hexadecimal (base-16) digits, displayed in 5 groups separated by hyphens, in the form 8-4-4-4-12 for a total of 36 characters (32 alphanumeric characters and 4 hyphens):

~~~
afe122be-59d3-4360-ad69-33c10108fa7a
~~~

The AMF_UUID column is optional. When present, it should indicate a path to the AMF file that is relative to the folder where the ALE file is located. The path hierarchy *MUST* not contain the parent folder or local folder distinguished values, i.e. ".." and "." to avoid any confusion.

The path and AMF file name must use characters from the set a-z, A-Z, 0-9, - (dash), _ (underscore) and ".".
No path segment shall use more than 128 characters and the total length shall not exceed 1024 characters.

## AMF_NAME

AMF_NAME shall be used to convey the AMF file name located in the same folder as the .edl source file:

~~~
clip_001.amf
~~~

The AMF_NAME is optional. When present, it should indicate the file name of the AMF document related to the clip. The AMF file must reside locally in the same folder as the EDL file. No sub-folder structure is permitted.

While AMF file can have any name, it is recommended to use the same base name as the clip file that the AMF document relates to. Moreover to ensure portability across file systems and operating systems it is recommended to use characters from the set a-z, A-Z, 0-9, - (dash), _ (underscore) and ".".

The AMF file name should use no more than 1024 characters.

## Linkage Rules

Since both AMF_UUID and AMF_NAME are optional, there are four possible combinations that can occur:

1. AMF_UUID and AMF_NAME are both absent

In this case, no AMF file can be associated with the clip

2. AMF_UUID is present and AMF_NAME is absent

In this case the host product must look for the corresponding AMF files into a database, using the UUID as a key to match the AMF file and the corresponding clip. Please note that the word "database" does not imply any specific implementation. This feature many not be supported by the host product

3. AMF_UUID is absent and AMF_NAME is present

In this case, the AMF_NAME column contains file names for AMF files that should be located at the same level in the file system (i.e. same folder) as the EDL file, or in a subfolder. The linkage is based on the file name and the UUID of the AMF files (if present) is ignored

4. AMF_UUID and AMF_NAME are both present

In this case, the host product can select between the methods described in 2) and 3). However, it is recommended to rely on the UUID in priority. The host product can provide and option to select the matching rule (UUID or file name). It is desirable to also provide a matching rule that checks both the UUID and file name.

### EDL event example:

~~~
...
010   Clip1   V     C          05:40:12:18 05:40:14:09 01:00:29:16 01:00:31:07
* AMF_NAME clip_001.amf
* AMF_UUID afe122be-59d3-4360-ad69-33c10108fa7a
...
~~~

## Remarks

1. Since each entry in the EDL file can use any of the combinations of AMF_UUID and AMF_NAME described above, it is recommended that the host product presents the issues encountered during the linkage and validation process as a log.

2. EDL files can carry inline ASC parameters. When using AMF with EDLs, the inline ASC parameters should be absent to avoid confusion, or ignored if present.

3. AMF files can have an optional aces:clipId element that is used to identify the clip that the AMF is related to. The aces:clipId element can carry a reference using different methods (e.g. file name, UUID, etc). It is strongly recommended that the clip identification method used in AMF correlates with the method used in the EDL files (e.g. file name).

If the same AMF file is shared by multiple clips, it is recommended to avoid the use of aces:clipId or ignore it.

A validation process can log any differences and present the results to the user of the product/tool processing the EDL+AMF files.
