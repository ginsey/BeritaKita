<?php

$brands = file('brand.txt', FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
$titles = file('title.txt', FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
$descriptions = file('desc.txt', FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);


$protocol = isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] === 'on' ? 'https' : 'http';
$host = $_SERVER['HTTP_HOST'];
$dir = rtrim(dirname($_SERVER['PHP_SELF']), '/'); 
$urlPathBase = "$protocol://$host$dir/"; 

$template = file_get_contents('template.php');

for ($i = 0; $i < count($brands); $i++) {
    
    if (isset($brands[$i], $titles[$i], $descriptions[$i])) {
        
        $output = str_replace('{{BRAND}}', $brands[$i], $template);
        $output = str_replace('{{TITLE}}', $titles[$i], $output);
        $output = str_replace('{{DESC}}', $descriptions[$i], $output);

        // Correct URL for canonical path
        $canonicalUrl = $urlPathBase . strtolower($brands[$i]) . '.html';
        $output = str_replace('{{urlPath}}', $canonicalUrl, $output);
        
        // Save output file with brand name in lowercase
        $filename = strtolower($brands[$i]) . '.html';
        file_put_contents($filename, $output);

        echo "Generated: $filename\n";
    }
}
?>
