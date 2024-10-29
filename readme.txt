=== Auto Smart Thumbnails ===
Contributors: longchaoliu 
Tags: smart thumbnails, downsize images, cleanup thumbnails, free up server storage, face detection, smart crop
Donate link: none
Requires at least: 3.8
Tested up to: 5.9
Requires PHP: 7.0
Stable tag: 1.1.4
License: GPLv3
License URI: http://www.gnu.org/licenses/gpl-3.0.html

Plugin creates thumbnails on demand with face detection. Remove unused thumbnails and downsizes images. Free up server storage.

== Description ==

= I. Face detection =

WordPress (WP) plugin/themes crop images per fixed position {top, center, bottom} x {left, center, right}. This often generates thumbnails with faces being cut out. This plugin (Auto Smart Thumbnails, AST) employs face detection to keep the face in the center of cropped images. 

= II. Downsize images =

There are many ways to backup/store your images. Your web server host is the last option for that though (too expensive). Essentially, your web server serves one purpose and one purpose only: a lean fast website. So making it small and agile is critical in both user experience and website maintenance. 

Media files (pdf, movie and images) are usually the biggest storage eaters. Here are some practice tips related to images:
1. Use jpg to store images. No png except for the logo images. 
2. Downsize your images to about (1920x1080, full high definition, FHD). 
3. Get rid of those unused thumbnails. 

AST helps you with 2 and 3. It helped to trim my website from 24G to 9G. 

AST downsizes big images in a smart way. It does so by a factor of an integer, e.g. 2, 3, 4 etc, so that the result image looks as crisp as the original on a webpage, e.g. an image of (5184x3456) is downsized by 3 to (1728x1152) and its file size is down from 4.9M to 239K. Conventional tools may downsize it by 3.2 (=3456/1080) to (1687x1080, short side exact FHD). Blurring happens because of the pixels fractioned. 

For images smaller than 3840x2160, which can't even be downsized by a factor of 2, they will be compressed (at WP default quality of 82%. Though the document says the default quality is 90%, in code it's 82%.)

= III. Cleanup thumbnails =

Some WP themes generate many, sometimes 10s of, custom sized thumbnails when an image is uploaded. These thumbnails may never be used yet take up your precious server storage space. AST helps remove these unused thumbnails and stop them from being generated when an image is uploaded. But a thumbnail will be generated and generated only when it is requested. The newly generated thumbnail is then stored for later use.

== Notes ==
AST is based on ['Optimize images Resizing' by OriginalEXE](https://wordpress.org/plugins/optimize-images-resizing/), which seems to be dormant for years.

[Face detection algorithm is by Maurice Svay](https://github.com/mauricesvay/php-facedetection). It returns only the first face candidate detected. For most of images it does the job well and and it's a bit faster than another implementation [PHP-FaceDetector by Felix Koch](https://github.com/felixkoch/PHP-FaceDetector). When it fails to detect face(s), the cropping will be done by the WordPress default.

The module is designed to be __extendable__. Other plugins can do face detection, e.g. with faster algorithms or better accuracy, or can designate focal points manually, then store the face/focal data to the meta data of an image. AST can pick up the data to do cropping. This is done by adding a new field 'focal_area' in the meta data, as below:

	Array (
		[width] => 512
		[height] => 512
		[file] => 2019/04/sample-image-file.jpg
		[sizes] => Array ()
		[focal_area] = (
			[x] => 100
			[y] => 123
			[w] => 58
            [h] => 58
			[faces] => Array (
				[tharavaad-svay] => Array (
					[0] => Array (
						[x] => 100
						[y] => 123
						[w] => 58
					)
				)
				[koch] => Array (
					[0] => Array (
						[x] => 100
						[y] => 123
						[w] => 58
					)
					[1] => Array (
						...
					)
				)
			)
		)
    )

The focal_area is defined by the position (x, y) and width and height. External plugins can store the detection result with these 4 parameters. AST can pick them up for cropping.

The 'focal_area' can be non-face objects that users want to focus on. Within it, the optional 'faces' array defines faces detected and the algorithm used.

To make it simple, some assumptions and numbers are defined as below:

1. To resave png images in jpg will save a lot space. But it needs to mess up with the WP database, which I stayed away for now. You may want to [convert your png images to jpg](https://www.xnview.com/en/xnviewmp/) before uploading them to your server. 

2. An image is downsized only when its short side > 2x1080. Otherwise it's re-compressed when its size >128k bytes. The new jpg file replaces the original only when it's 25k bytes smaller.

3. When a downsizing happens, the original is saved in uploads/ast-backup. The year/month structure is preserved. To save the server storage space, it's recommended to download it and delete it from the server. 

I didn't get time to handle the localization language files yet. 

Please let me know how it works for you, or any improvement suggestions or feedback. Thanks!

[Source code](https://github.com/longchaoliu/auto-smart-thumbnails)
[Discord forum](https://discord.gg/8ekYwzv)

== Installation ==

In WP 'Add Plugins', seach for 'auto-smart-thumbnails' and 'Install Now', and activate it. 

== Frequently Asked Questions ==

**I just installed the plugin. Is there anything else I need to do?**

No. AST works silently in the background. If your site has many existing thumbnails, you can remove them manually by 'Tools -> Auto Smart Thumbnails' or 'Installed Plugins -> Auto Smart Thumbnails -> Remove Unused Thumbnails'. 

**Some image sizes are not cleaned, why?**

AST doesn't remove the default thumbanil which is defined by WP and WP uses it frequently.

**How do I know which files the plugin cleaned up?**

A list of removed files is available after a cleanup, When a cleanup is done, a message will show how many images is removed. Click on the number to show the list.

**Are there any drawbacks to using AST?**

Not as I know. Your WP website will continue working as is. But your uploads folder will be lighter. It helps save your server storage and makes backup easier and faster. 

**I run into problems. What can I do?**
Turn on debug and you can check what's going on. Check 'Log debug info for troubleshooting' in 'Settings -> Auto Smart Thumbnails'. After that, you will see a button 'Get Debug Log' in 'Tools -> Auto Smart Thumbnails'. It shows up when there is info logged after a cleanup is done. Leave a note with your url and the logged info in the support forum and I will try my best to help. Or you can join [Discord forum](https://discord.gg/8ekYwzv) to see if I or anyone there can help.

**It seems to be stuck on "Cleanup in progress..." What can I do?**
This is because your server doesn't respond. AST needs to know how many images need to be processed. When it happens, as explained above, turn on debug, leave a note with the url in the support forum.

**I still see a lot of big png images, what can I do?**
To downsize or re-compress image png files to jpg needs to change database. To keep the data and files in sync needs a lot messy code. So I decided not to do it. 

My suggestion is to use [XnViewMP](https://www.xnview.com/en/xnviewmp/) to convert the png to jpg before uploading it to your media library. XnViewMP can do batch coversion and amazing color correction. It got only one drawback that it can't decide the target size intellgiently as AST does. You need to manually calculate the size to downsize to and different images may need seperate calculations. 

**Have you checked smartcrop plugins currently available?**
At the moment, there are two plugins: ['SmartCrop'](https://wordpress.org/plugins/smartcrop/) by late Alex Mills (Viper007Bond) and ['WP SmartCrop'](https://wordpress.org/plugins/wp-smartcrop/) by 'Burlington Bytes'. The later provices a tool for users to set a focal point manually. Alex's SmartCrop plugin uses [Smart Cropping Class/algorithm](https://github.com/gschoppe/PHP-Smart-Crop/blob/master/smart_crop.php) developed by Greg Schoppe, which is based on color difference and image entropy and puts the focus of the image at or close to the photograph rule of thirds line. It doesn't do face detection. AST is the __first__ and __only__ WordPress plugin that does smart crop with face detection. 

I do hope it inspires people and more people join to explore the latest technologies and make it helpful, and more fun. 

**How about WordPress's new big image feature in 5.3?**
WordPress 5.3 introduces a new way to manage a big image by checking if its height or its width is above a big_image threshold (the default value is 2560px), and scaling down it to big_image. This is a nice feature that WordPress has big images in check. 

AST does it in a different and better way. It scales down images by a factor of an integer which means no fraction of pixcels and no bluring. And WordPress checks only new uploads. AST will check all the images, existing or new. 

Note the new feature in WordPress 5.3+ will automatically scale down large pictures. So the downsize function in AST won't be triggered.

== Screenshots ==

1. Thumbnails generated by WP default. Faces cut. 
2. Thumbnails generated by AST plugin. Faces detected and preserved.
3. Thumbnails generated by SmartCrop plugin. Faces cut. 
4. Admin UI added by the plugin.
5. Log page after a cleanup.
6. Thumbnails before cleanup. 
7. Thumbnails after cleanup. 

== Upgrade Notice ==
none

== Changelog ==
= 1.1.4 =
1. Fixed a crash with is_resource in PHP 8. PHP doesn't return resoure, changed to object.
2. Tested up to WordPress 5.9.

= 1.1.3 =
1. Save debug log file for remote debugging.
2. Tested up to WordPress 5.7.

= 1.1.2 =
1. Add debug log file for remote debugging.
2. Tested up to WordPress 5.4.

= 1.1.1 =
1. Test up to WorePress 5.3.2.
2. Correct readme typos and wording.

= 1.1.0 =
1. Add face detection.
2. Add debug info log for troubleshooting.

= 1.0.0 =
Initial version. Based on 'Optimize Images Resizing' by OriginalEXE. Major issues fixed: 
1. During pagination, WP_Query returns corrupted result (duplicate and missing post IDs).
2. Nothing happends when button 'Start new cleanup' is clicked.
3. Sizes in meta data and thumbnail files out of sync. 
