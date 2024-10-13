# EnigmaXML Documentation

Unofficial documentation for the internal EnigmaXML format used by Finale in files.

Finale `.musx` files are actually `gzip` archives of a directory with the following structure.

```
/META-INF
    container.xml
mimetype
NotationMetadata.xml
score.dat
```

The `score.dat` file must be extracted using a utility such as the [denigma CLI tool](https://github.com/chrisroode/denigma). The extracted version of the file contains the Finale Enigma data in xml format. This repo maintains a set of markdown files to document what is known about it.


## EnigmaXML Format

There is no official documentation of the EnigmaXML format. This utility is a tool to aid third parties in analyzing and documenting it. This repository also maintains documentation pages that describe what has been discovered so far.

See the [documentation](docs/enigmaxml.md) page.

## Other Resources

Many of the tags in EnigmaXML correspond to classes and properties in the [Finale PDK Framework](https://pdk.finalelua.com/). To the extent these can be mapped to the XML, those links into the PDK Framework documentation are also listed. While the PDK Framework is intended for Lua plugins, much of it is also relevant to the fields in EnigmaXML.

Another tool that may help decipher the format is the free [Enigma Text Dump](https://robertgpatterson.com/-fininfo/-downloads/download-free.html) plugin for Finale. It spits out a text file of all the data it can find in the document in a format similar to the old Enigma Text File format. Where known, the output includes references to the corresponding PDK Framework class names.

