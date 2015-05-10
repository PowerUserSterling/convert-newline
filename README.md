# convert-newline

[![Build Status](https://travis-ci.org/takenspc/convert-newline.svg?branch=master)](https://travis-ci.org/takenspc/convert-newline)
[![Coverage Status](https://coveralls.io/repos/takenspc/convert-newline/badge.svg?branch=master)](https://coveralls.io/r/takenspc/convert-newline?branch=master)

Unify newline characters to either `\n`,`\r\n`, or `\r`.

## Usage

### String

	var convertNewline = require("convert-newline");
	
	var converter = convertNewline("lf").string();
	converter("AAA\rBBB\r\nCCC\n"); // -> "AAA\nBBB\nCCC\n"

### Buffer

	var convertNewline = require("convert-newline");
	var buffer = new Buffer("AAA\rBBB\r\nCCC\n");
	
	var converter = convertNewline("lf").buffer();
	var newBuffer = converter(buffer);
	
	newBuffer.toString(); // -> "AAA\nBBB\nCCC\n"

Converting non-utf8 encoded buffers is supported.

	var iconv = require("iconv-lite");
	var convertNewline = require("convert-newline");
	var buffer = iconv.encode(new Buffer("あ\rい\r\nう\n"), "shift_jis");
	
	var converter = convertNewline("lf", "shift_jis").buffer();
	var newBuffer = converter(buffer);
	
	iconv.decode(newBuffer, "shift_jis"); // -> "あ\nい\nう\n"

### Stream

	var fs = require("fs");
	var convertNewline = require("convert-newline");
	var reader = fs.createReadStream("foo.txt", { encoding: "utf8" });
	var writer = fs.createWriteStream("bar.txt");
	
	var converter = convertNewline("lf").stream();
	reader
		.pipe(converter)
		.pipe(writer);

To convert non-utf8 encoded streams, you can use [iconv-lite](https://github.com/ashtuchkin/iconv-lite/).

	var fs = require("fs");
	var iconv = require("iconv-lite");
	var convertNewline = require("convert-newline");
	var reader = fs.createReadStream("foo.txt");
	var writer = fs.createWriteStream("bar.txt");
	
	var converter = convertNewline("lf").stream();
	reader
		.pipe(iconv.decodeStream("shift_jis"))
		.pipe(converter)
		.pipe(iconv.encodeStream("shift_jis"))
		.pipe(writer);


## API

### convertNewline(newline, encoding)

#### newline

Target newline characters. Either `"lf"`, `"crlf"`, or `"cr"`.

### encoding

Optional. The encoding of the buffer.

[Supported encodings](https://github.com/ashtuchkin/iconv-lite/wiki/Supported-Encodings) are listed on the [iconv-lite](https://github.com/ashtuchkin/iconv-lite/) wiki.