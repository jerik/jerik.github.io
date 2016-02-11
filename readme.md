# jerik.github.io
snippset and things I want to capture / document

## todos
- Integrate Navigation dropdown with my pages. The page names should be stated in a tag ( meta-tag, own-tag.. ), so that it can be read by js. 
 - For Layout see: https://bootswatch.com/cerulean/
 - For integration see: https://github.com/arturadib/strapdown/blob/gh-pages/src/strapdown.js search for navbar
- perhaps integrate gist, so that I can download the gist and save it in the jerik.github.io? Easy to integrate, but I do not have the gist-code in this git repo...
- To get the markdown files automated. Try to read the \*.md files and shove them into the textarea field? I have access to files if I know the name e.g. readme.md.
  Currently I get the message "readme.md ist nicht wohlgeformt". See ondex.html
	$.get( "readme.md", function( data ) {
	  alert( "" + data + "" );
	});
	$.ajax({
	  method: "GET",
	  url: "readme.md",
	  dataType: "text",
	  function( data ) { alert( data ); }
	});

