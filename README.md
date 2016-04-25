# GoogleDrive
Mount GoogleDrive with elFinder

elFinder Connector Configuration
================================

/**
•# Google Drive volume driver need "google-api-php-client 1.1.7" package"
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

====================================================================

elfinder.full.js
=================

googledrive: {
				name : 'GoogleDrive.com',
				inputs: {
					//offline  : $('<input type="checkbox"/>').on('change', function() {
//						$(this).parents('table.elfinder-netmount-tb').find('select:first').trigger('change', 'reset');
//					}),
					host     : $('<span><span class="elfinder-info-spinner"/></span><input type="hidden"/>'),
					path     : $('<input type="text" value="/"/>'),  // change value root to /
					user     : $('<input type="hidden"/>'),
					pass     : $('<input type="hidden"/>')
				},
				select: function(fm, ev, data){
					var f = this.inputs, oline = f.offline,
						data = data || null;
					if ($(f.host[0]).find('span.elfinder-info-spinner').length || data === 'reset') {
						//if (oline.parent().children().length === 1) {
//							f.path.parent().prev().html(fm.i18n('folderId'));
//							oline.attr('title', fm.i18n('offlineAccess'));
//							oline.uniqueId().after($('<label/>').attr('for', oline.attr('id')).html(' '+fm.i18n('offlineAccess')));
//						}
						$(f.host[0]).empty().addClass('elfinder-info-spinner')
							.parent().find('span.elfinder-button-icon').remove();
						fm.request({
							data : {cmd : 'netmount', protocol: 'googledrive', host: 'googledrive.com', user: 'init', pass: 'init', options: {url: fm.uploadURL, id: fm.id}},  //add pass : 'init'
							preventDefault : true
						}).done(function(data){ 
							$(f.host[0]).removeClass("elfinder-info-spinner").html(data.body.replace(/\{msg:([^}]+)\}/g, function(whole,s1){return fm.i18n(s1,'GoogleDrive.com');}));
						}).fail(function(){});
					} else {
						oline.parent().parent()[f.user.val()? 'hide':'show']();
					}
				},
				done: function(fm, data){
					var f = this.inputs, p = this.protocol;
					
					if (data.mode == 'makebtn') {
						$(f.host[0]).removeClass('elfinder-info-spinner');
						f.host.find('input').hover(function(){$(this).toggleClass('ui-state-hover');});
						$(f.host[1]).val('');
						f.path.val('/').next().remove();    // change root to /
						f.user.val('');
						f.pass.val('');
						//f.offline.parent().parent().show();
					} else {
						$(f.host[0]).html('GoogleDrive.com&nbsp;').removeClass('elfinder-info-spinner');
						if (data.reset) {
							p.trigger('change', 'reset');
							return;
						}
						$(f.host[0]).parent().append($('<span class="elfinder-button-icon elfinder-button-icon-reload" title="'+fm.i18n('reAuth')+'">')
							.on('click', function() {
								$(f.host[1]).val('reauth');
								p.trigger('change', 'reset');
							}));
						$(f.host[1]).val('googledrive');
						if (data.folders) {
							f.path.next().remove().end().after(
								$('<div/>').append(
									$('<select class="ui-corner-all" style="max-width:200px;">').append(
										$($.map(data.folders, function(n,i){return '<option value="'+i+'">'+fm.escape(n)+'</option>'}).join(''))
									).on('change', function(){f.path.val($(this).val());})
								)
							);
						}
						f.user.val('done');
						f.pass.val('done');
						f.offline.parent().parent().hide();
					}
				},
				fail: function(fm, err){
					this.protocol.trigger('change', 'reset');
				}
			}
		},
