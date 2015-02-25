mms uri 
```  
table-----------------------------------------uri------------------(sql) 
sms---------------------------Sms.CONTENT_URI-----------------"content://sms"(select * from sms;)
sms---------------------------Sms.Inbox.CONTENT_URI-----------"content://sms/inbox"(select * from sms where type=1)
sms---------------------------Sms.Sent.CONTENT_URI------------"content://sms/sent" (select * from sms where type=2)
sms---------------------------Sms.Draft.CONTENT_URI-----------"content://sms/draft"(select * from sms where type=3)
sms---------------------------Sms.Outbox.CONTENT_URI----------"content://sms/outbox"(select * from sms where type=4)
sms--------------------------------------------------"content://sms/undelivered"(select * from sms where type in(4,5,6))
sms--------------------------------------------------"content://sms/failed"(select * from sms where type =5)
sms--------------------------------------------------"content://sms/queued"(select * from sms where type =6)
raw---------------------------"content://sms/raw"-------------(select * from raw;)
attachments-------------------"content://sms/attachments"-----(select * from attachments)
``` 
表－－－Uri
media数据库分为内外：内部卷标为：internal 外部卷标为：external 用*代替。
query:
```  
images --------------------MediaStroe.Images.Media.EXTERNAL_CONTENT_URI--------------"content://media/*/images/media"
images/_id ----------------MediaStroe.Images.Media.EXTERNAL_CONTENT_URI--------------"content://media/*/images/media/"
thumbnails-----------------MediaStore.Images.Thumbnails.EXTERNAL_CONTENT_URI---------"content://media/*/images/thumbnails"
thumbnails/_id------------MediaStore.Images.Thumbnails.EXTERNAL_CONTENT_URI---------"content://media/*/images/thumbnails/"
audio ---------------------MediaStore.Audio.Media.EXTERNAL_CONTENT_URI---------------"content://media/*/audio/media"
audio/_id -----------------MediaStore.Audio.Media.EXTERNAL_CONTENT_URI---------------"content://media/*/audio/media/"
audio_genres---------------MediaStore.Audio.Genres.EXTERNAL_CONTENT_URI--------------"content://media/*/audio/genres"
audio_genres/_id-----------MediaStore.Audio.Genres.EXTERNAL_CONTENT_URI--------------"content://media/*/audio/genres/"
audio_audio_playlists------MediaStore.Audio.Playlists.EXTERNAL_CONTENT_URI-----------"content://media/*/audio/playlists"
audio_audio_playlists/_id--MediaStore.Audio.Playlists.EXTERNAL_CONTENT_URI-----------"content://media/*/audio/playlists/"
video----------------------MediaStore.Video.Media.EXTERNAL_CONTENT_URI---------------"content://media/*/video/media"
video/_id------------------MediaStore.Video.Media.EXTERNAL_CONTENT_URI---------------"content://media/*/video/media/"
videothumbnails------------MediaStore.Video.Thumbnails.EXTERNAL_CONTENT_URI----------"content://media/*/video/thumbnails"
videothumbnails/_id-------MediaStore.Video.Thumbnails.EXTERNAL_CONTENT_URI----------"content://media/*/video/thumbnails/"
artist_info----------------MediaStore.Audio.Artists.EXTERNAL_CONTENT_URI-------------"content://media/*/audio/artist"
artist_info/_id------------MediaStore.Audio.Artists.EXTERNAL_CONTENT_URI-------------"content://media/*/audio/artist/"
album_info-----------------MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI--------------"content://media/*/audio/album"
album_info/_id-------------MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI--------------"content://media/*/audio/album/"
album_art------------------      
```          