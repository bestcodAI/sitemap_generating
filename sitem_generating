<?php

/**
 * Site map generator
 */
// Setup
$s = microtime(TRUE);
set_time_limit(0);
ob_start();

/** Site URL **/
define( 'url', 'http://---.com/'); // obfuscated for public forum

/** Sitemap file with full path **/
$xmlfile = '/home/---/public_html/sitemap.xml'; // obfuscated for public forum

// URLs to add to put in the sitemap
$urls = array(url);

// Fill the array
getUrls(url,$urls);

// xml starter
$xml = '<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">'."\r\n";

// pattern to use for each page
$pagePatt = "\t<url>\r\n\t\t<loc>%s</loc>\r\n\t\t<changefreq>%s</changefreq>\r\n\t</url>\r\n";

// Change frequency options
$freqs = array('weekly','weekly','monthly');
foreach( $urls as $url ) {
   // determine how often the current page "might" change
   $freq = substr_count($url,'/') - 3;
   // add current page to the sitemap
   $xml .= sprintf($pagePatt,$url,$freqs[$freq]);
}

// xml closer
$xml .= '</urlset>';

// Attempt to put the new sitemap in the file
if( file_put_contents($xmlfile,$xml) ) {
   // New sitemap success!
   echo 'Generated new sitemap at '.$xmlfile.' on '.date('Y-m-d')."\n";
} else {
   // New sitemap failure :(
   echo 'Failed to generate new site map on '.date('Y-m-d')."\n";
}

// Log this scripts results
$f = fopen('sitemap.log','a');

// Elapsed time for sitemap creation
$e = microtime(TRUE);
$tot = $e - $s;
echo 'Completed sitemap in '.$tot."\n";

// Turn the output into log contents
$out = ob_get_clean();
fputs($f,$out);
fclose($f);


function getUrls($url,&$urls) {
   // Get the page contents from the supplied $url
   $page = getCurlContents($url);
   sleep(5);

   // Get all the links on the page
   if( preg_match_all('/href="([^"]+)"/i',$page,$matches) ) {
      foreach( $matches[1] as $match ) {
         // Check if the link is for this site
         if( strpos($match,url) === FALSE ) continue;
         // Check if the link is a resource
         if( preg_match('/\.(css|js|jpg|jpeg|bmp|gif|zip)/i',$match) ) continue;
         // Check if the link is already in the current result array
         if( !in_array($match,$urls) ) {
            // add link to the result array
            $urls[] = $match;
            // Page hasn't been parsed, get the links on that page (Recurse this function)
            getUrls($match,$urls);
         }
      }
   }
}

// Use curl to get the contents of a $url
function getCurlContents($url) {
   // Create curl handle
   $ch = curl_init();
   // Set curl options
   curl_setopt($ch, CURLOPT_URL,$url);
   curl_setopt($ch, CURLOPT_HEADER, 0);
   curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
   curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
   curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);
   curl_setopt($ch, CURLOPT_USERAGENT, 'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:9.0.1) Gecko/20100101 Firefox/9.0.1'); 
   curl_setopt($ch, CURLOPT_HEADER, false);
   // get the contents
   $contents = curl_exec($ch);
   // close the handle
   curl_close($ch);
   // return the contents
   return $contents;
}
