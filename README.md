# instagram-api

What is the Instagram API?

Insagram API is an advanced API for managing multiple user-based accounts.

# How does the API work?

The code below pulls the user information!

```sh
$current = $ig->account->getCurrentUser();
print_r($current->getUser());
```

Change profile bio!

```sh
echo $ig->account->setBiography('Profile info');
```

To edit account information

```sh
echo $ig->account->editProfile(
    'http://www.fatihsayin.com.tr', // Link
    '+901234567891', // Phone
    "Fatih Sayın", // Name
    'Fatih SAYIN Bio', // Bio
    'info@fatihsayin.com', // mail
    1, // gender -1 male -2 female -3 unknown
    'fatihsyn' // url
);
```

First line to change profile picture, second line to remove!

```sh
echo $ig->account->changeProfilePicture('images/profile.jpg');
echo $ig->account->removeProfilePicture();
```

Used to toggle profile visibility

```sh
echo $ig->account->setPrivate();
echo $ig->account->setPublic();
```
Change account password

```sh
echo $ig->account->changePassword('old password', 'new password');
```

# Timeline

To share a photo

```sh
$photo = __DIR__ . '/images/photo.jpg';
$metadata = [
    'caption' => 'This photo was uploaded using the api!'
];

$photo = new \InstagramAPI\Media\Photo\InstagramPhoto($photo);
echo $ig->timeline->uploadPhoto($photo->getFile(), $metadata);
```

To share a video

```sh
\InstagramAPI\Media\Video\FFmpeg::$defaultBinary = '/usr/local/bin/ffmpeg';
\InstagramAPI\Utils::$ffprobeBin = '/usr/local/bin/ffprobe';

$video = __DIR__ . '/video/videoname.MP4';
$metadata = [
    'caption' => 'This photo was uploaded using the api!'
];
$video = new \InstagramAPI\Media\Video\InstagramVideo($video);
echo $ig->timeline->uploadVideo($video->getFile(), $metadata);
```
To share Photo/Video as an album

```sh
$album = [
    [
        'type' => 'photo',
        'file' => __DIR__ . '/images/photo.jpg'
    ],
    [
        'type' => 'video',
        'file' => __DIR__ . '/video/videoname.MP4'
    ]
];

$metadata = [
    'caption' => 'This album was uploaded using api'
];

$mediaOptions = [
    'targetFeed' => \InstagramAPI\Constants::FEED_TIMELINE_ALBUM
];
foreach ($album as &$item) {
    $validMedia = null;
    switch ($item['type']) {
        case 'photo':
            $validMedia = new \InstagramAPI\Media\Photo\InstagramPhoto($item['file'], $mediaOptions);
            break;
        case 'video':
            $validMedia = new \InstagramAPI\Media\Video\InstagramVideo($item['file'], $mediaOptions);
            break;
        default:
            // bilinmeyen türleri görmezden gel.
    }
    if ($validMedia === null) {
        continue;
    }
    try {
        $item['file'] = $validMedia->getFile();
        $item['__media'] = $validMedia;
    } catch (\Exception $e) {
        continue;
    }
    if (!isset($mediaOptions['forceAspectRatio'])) {
        $mediaDetails = $validMedia instanceof \InstagramAPI\Media\Photo\InstagramPhoto
            ? new \InstagramAPI\Media\Photo\PhotoDetails($item['file'])
            : new \InstagramAPI\Media\Video\VideoDetails($item['file']);
        $mediaOptions['forceAspectRatio'] = $mediaDetails->getAspectRatio();
    }
}
unset($item);

echo $ig->timeline->uploadAlbum($album, $metadata);
```

To list content shared by any user

```sh
echo $ig->timeline->getUserFeed('id'); //User İD
echo $ig->timeline->getSelfUserFeed();
```

To list the content of users you follow

```sh
echo $ig->timeline->getTimelineFeed();
```

It is used to archive or extract a content you have shared.

```sh
echo $ig->timeline->archiveMedia('media_id'); //(ADD TO ARCHIVE)
echo $ig->timeline->archiveMedia('media_id', 'PHOTO', false); // (DELETE TO ARCHIVE)
```
To list archived content

```sh
echo $ig->timeline->getArchivedMediaFeed()
```

To back up account contents

```sh
echo $ig->timeline->backup('yedek klasör yolu');
```
# Media

```sh

```


```sh

```

```sh

```



