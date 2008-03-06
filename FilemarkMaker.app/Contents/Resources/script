#!/usr/bin/perl
#
# FilemarkMaker 1.0
# generates filemarks (data: URIs) out of passed/dropped files
# 
# by Jamie Wilkinson <http://tramchase.com/blog> 
# and David Keay <http://insanelygreattees.com>
# produced in 2 hours at Jelly, July 19th, 2007 NYC
#
# Creative Commons Attribution-Noncommercial-Share Alike 3.0 License
#
# usage: filemark-maker.pl <filename>
# formats: just JPG, PNG, GIF, PDF, DOC, XLS, PPT explicitly for now...
#

use MIME::Base64; 
$appname = 'FilemarkMaker';

# sanity check
$file = $ARGV[$#ARGV]; # compatible w/ commandline AND platypus
$basename = `basename $file`;
if( $basename =~ /$appname/) { # weird condition -- platypus has argv like 'FilemarkMaker.app filename.gif', but CLI is just 'filename.gif'
  $error = 'Whoops! Use this by dropping a file on the app.';
  `osascript -e 'tell application "$appname"' -e 'activate' -e 'display dialog "$error"' -e 'end tell'`;
  die($error);
}

# process input
open(TEXT, $file) || die("Could not open infile");
@raw_data=<TEXT>;
close(TEXT);
$text = join '', @raw_data;
$text = encode_base64($text."\n");
$text =~ s/\s+//g;
$file =~ /(.*)\.(.*)$/;
$ext = $2;
chomp $ext;
$ext = lc $ext;

# extension => content-type hardcodes
# does the perl MIME module do anything with this? TODO
$content_type = 'application/octet-stream';
if($ext eq 'gif') {
  $content_type = 'image/gif';
} elsif($ext eq "jpg" || $ext eq "jpeg") {
  $content_type = 'image/jpeg';
} elsif($ext eq 'png') {
  $content_type = 'image/png';
} elsif($ext eq 'pdf') {
  $content_type = 'application/pdf';
} elsif($ext eq 'html' || $ext eq 'htm' || $ext eq 'shtml' || $ext eq 'php') {
  $content_type = 'text/html';
} elsif($ext eq 'txt') {
  $content_type = 'text/plain';
} elsif($ext eq 'rtf' || $ext eq 'rtfd') {
  $content_type = 'application/rtf';
} elsif($ext eq 'doc') { # M$ stuff
  $content_type = 'application/msword';
} elsif($ext eq 'ppt') {
  $content_type = 'application/vnd.ms-powerpoint';
} elsif($ext eq 'xls') {
  $content_type = 'application/vnd.ms-excel';
}


# generate output
$text = "data:$content_type;charset=utf-8;base64,".$text;
# $text = "data:$content_type,".$text;

$linktext = "$basename";  # text for the data: URI
$step1 = "<span class=\"step\">Step 1</span><br /> Right-click this link and choose 'Add link to bookmarks'<br />";
$step2 = "<span class=\"step\">Step 2</span><br /> Sync your iPhone";
$step3 = "<span class=\"step\">Step 3</span><br /> On the iPhone click the bookmark to view the file!";
$out = "
  <html><head>
    <title>$linktext</title>
    <style type=\"text/css\">
    body { color: #999; background-color: #000; text-align: center; padding-top: 230px; font-family: sans-serif; } 
    a { color: #fff; text-decoration: none; border-bottom: 1px dotted #fff; } 
    a:hover { color: #f0f0f0 } #footer { margin-top: 200px;font-size: smaller; } 
    span.step { text-transform: uppercase; font-size: 7pt; color: #777; }
    </style>
  </head><body>

  $step1
  <h1><a href=\"$text\">$linktext</a></h1>
  <br />
  $step2
  <br /><br />
  $step3
  
  <div id=\"footer\">
  $appname by <a href=\"http://insanelygreattees.com\">Insanely Great Tees</a> and <a href=\"http://tramchase.com\">Jamie Wilkinson</a>
  </div>
</body></html>";

# write output & open in Safari (or add bookmark automatically)
$outfile = '/tmp/FilemarkMakerOutput.html';
open(OUT,">$outfile") || die("Cannot open outfile");
print OUT $out;
`open -a Safari $outfile`;