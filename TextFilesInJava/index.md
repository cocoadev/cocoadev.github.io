---
layout: page
title: TextFilesInJava
---

I am at a loss to figure out how to read a text file into a String in Java. I am new to Java, so maybe I am just really missing something in the documentation. Also, I was wondering if anybody has step by step instructions on how to get the Java Bridge working in Cocoa (using both Objective-C and Java classes in the same project).

-- General/JamesGlenlake

----

    

String readFile( String filePath ) {
	General/StringBuffer fileContents = new General/StringBuffer();

	try {
		General/BufferedReader reader = new General/BufferedReader(
			new General/FileReader(filePath)
		);

		String line;
	
		while ( (line = reader.readLine()) != null ) {
			fileContents.append(line);
		}
	} catch ( General/IOException ioe ) {
		// handle the exception properly in the real application
		return null;
	} finally {
		reader.close();
	}

	return fileContents.toString();
}


I suggest looking up the documentation for     General/FileReader,     General/BufferedReader and look at the Java Tutorial on http://java.sun.com

-- General/TheoHultberg/Iconara

----

The documentation for General/BufferedReader says that it does not include the line terminator in readLine(). Should I append a     "\n" with each line as well?

-- General/JamesGlenlake

----

If you need it, yes.
