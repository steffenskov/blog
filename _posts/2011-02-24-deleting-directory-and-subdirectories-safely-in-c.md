---
id: 166
title: 'Deleting directory and subdirectories safely in C#'
date: '2011-02-24T17:35:18+01:00'
author: Steffen
excerpt: "Most C# developers know of Directory.Delete(path, recursive). However every once in a while they'll discover that it's error prone.\r\n\r\nHere's how to delete directories with read-only files inside them."
layout: post
guid: 'http://www.ckode.dk/?p=166'
permalink: /programming/deleting-directory-and-subdirectories-safely-in-c/
categories:
    - Programming
tags:
    - 'C#'
    - Optimization
---

Most C# developers know of Directory.Delete(path, recursive). However every once in a while they’ll discover that it’s error prone.

The thing is, it doesn’t handle read-only files very well… In fact it throws an exception when noticing the first read-only file or sub-directory.

The only way to handle this, is by manually setting the files and sub-directories to not read-only.

A simple recursive function which handles this could look like this:

```

private static void DeleteFileSystemInfo(FileSystemInfo fsi)
{
	fsi.Attributes = FileAttributes.Normal;
	DirectoryInfo di = fsi as DirectoryInfo;

	if (di != null) // it's a directory
		foreach (FileSystemInfo childFsi in di.GetFileSystemInfos())
			DeleteFileSystemInfo(childFsi);

	fsi.Delete();
}
```

And whilst this does indeed work, it’s very slow 🙁

In fact it’s so slow I was asked to find a faster method. Luckily it’s actually rather easy to speed it up quite a bit.

All you have to do is remove read-only in one go, and \*then\* use Directory.Delete(path, recursive). Like this:

```

private static void DeleteFolderRecursive(DirectoryInfo baseDir)
{
	baseDir.Attributes = FileAttributes.Normal;
	foreach (var childDir in baseDir.GetDirectories())
		DeleteFolderRecursive(childDir);
	
	foreach (var file in baseDir.GetFiles())
		file.IsReadOnly = false;

	baseDir.Delete(true);
}
```

How much faster do you think it is ?

My benchmarks says it’s roughly 2,5 times faster when deleting 1.000 files in 1.000 folders (that’s 1.000 files in each folder = 1.000.000 files in total)

Quite a nice improvement I think 🙂