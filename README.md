# GoogleDrive
Mount GoogleDrive with elFinder

elFinder Connector Configuration
================================

/**
•# Google Drive volume driver need "google-api-php-client" package"
•* google-api-php-client: https://github.com/google/google-api-php-client/releases 
•* copy directory "google-api-php-client" and paste in elFinder php directory
•* Google Drive developer console: https://console.developers.google.com 
•* Create login in google developer console and create app and get clientid, clientsecret, redirecturi
•* create new project ->Enable and manage APIs ->Drive API ->Enable ->Goto Credentials
•* Create Credentials ->oAuth Client ->web Applicaton
•* Application Name Optional, Authorized redirect URIs should be your elFinder connector path
•* copy oAuth client ID and client secret */ // Required for drive.google.com connector support

// Required for drive.google.com connector support
 include_once dirname(FILE).DIRECTORY_SEPARATOR.'elFinderVolumeGoogleDrive.class.php';

// Google Drive driver need next three settings. You can get at https://console.developers.google.com
 define('ELFINDER_GOOGLEDRIVE_CLIENTID', ' ');
 define('ELFINDER_GOOGLEDRIVE_CLIENTSECRET', ' ');
 define('ELFINDER_GOOGLEDRIVE_REDIRECTURI', 'http://localhost:8080/elfinder/php/connector.minimal.php');

if (isset($_GET['code'])) {
 $callback = new elFinderVolumeGoogleDrive();
 $callback->netmountPrepare(array()); //prepare callback url
 exit();
 }

