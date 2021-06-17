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
To get details of a shared content

```sh
echo $ig->media->getInfo('medya_id');
```
To delete a shared content

```sh
echo $ig->media->delete('medya_id', 'content_type'); ( PHOTO|VIDEO|ALBUM )
```
To edit a shared content

```sh
echo $ig->media->edit('medya_id', 'new statement');

```
Like / Unlike

```sh
echo $ig->media->like('medya_id');
echo $ig->media->unlike('medya_id');
```
To list liked content
```sh
echo $ig->media->getLikedFeed();
```
To get the likes list of the content

```sh
echo $ig->media->getLikers('medya_id');
```

Enable / disable comments for shared content

```sh
echo $ig->media->enableComments('medya_id');
echo $ig->media->disableComments('medya_id');
```
To get the comments list of the content

```sh
echo $ig->media->getComments('medya_id');
```

to comment
```sh
echo $ig->media->comment('medya_id', 'comment');
```
To reply to a comment

```sh
$ig->media->comment('medya_id', '@username text', 'comment_id')
```

To get the comment replies list of the content

```sh
echo $ig->media->getCommentReplies('medya_id', 'yorum_id')

```
Delete comment

```sh
echo $ig->media->deleteComment('medya_id', 'comment_id');

echo $ig->media->deleteComments('medya_id', ['comment_id1', 'comment_id2']);
```
Comment Like / Unlike

```sh
echo $ig->media->likeComment('Comment_id');
echo $ig->media->unlikeComment('Comment_id');
```

To get the comment like list 

```sh
echo $ig->media->getCommentLikers('yorum_id');

```
Content Save / UnSave

```sh
echo $ig->media->save('medya_id');
echo $ig->media->unsave('medya_id');
```
To list recorded content

```sh
echo $ig->media->getSavedFeed();
```
# User

To get user details
```sh
echo $ig->people->getInfoById('user_id');
echo $ig->people->getInfoByName('user_adi');
echo $ig->people->getSelfInfo();
```
User listing by algorithm
```sh
echo $ig->people->getBootstrapUsers();
```
To list follow requests
```sh
echo $ig->people->getPendingFriendships();
```
Accept / Do Not Accept Followers
```sh
echo $ig->people->approveFriendship('user_id');
echo $ig->people->rejectFriendship('user_id');
```
delete followers
```sh
echo $ig->people->removeFollower('user_id');
```
To list your followers
```sh
echo $ig->people->getFollowing('user_id', \InstagramAPI\Signatures::generateUUID());
echo $ig->people->getSelfFollowing(\InstagramAPI\Signatures::generateUUID());
```
To list your followers
```sh
echo $ig->people->getFollowers('user_id', \InstagramAPI\Signatures::generateUUID());
echo $ig->people->getSelfFollowers(\InstagramAPI\Signatures::generateUUID());
```
User Search
```sh
echo $ig->people->search('fatihsyn');
```
Discover users
```sh
echo $ig->people->discoverPeople();
```
List suggested followers
```sh
echo $ig->people->getSuggestedUsers('user_id');
```
User follow / unfollow
```sh
echo $ig->people->follow('user_id');
echo $ig->people->unfollow('user_id');
```

----

To add the user to favorites
```sh
echo $ig->people->favorite('user_id');
echo $ig->people->unfavorite('user_id');
```
To be notified when the user posts a Story
```sh
echo $ig->people->favoriteForStories('user_id');
echo $ig->people->unfavoriteForStories('user_id');
```
To report a user as spam
```sh
echo $ig->people->report('user_id');
```
To block / unlock a users account
```sh
echo $ig->people->block('user_id');
echo $ig->people->unblock('user_id');
```
To hide or show media and stories shared by a user
```sh
echo $ig->people->muteUserMedia('user_id', 'story|post|all');
echo $ig->people->unmuteUserMedia('user_id', 'story|post|all');
```
For a list of people you ve blocked
```sh
echo $ig->people->getBlockedList()
```
To block or unblock someone you dont want to see the stories you share
```sh
echo $ig->people->blockMyStory('user_id');
echo $ig->people->unblockMyStory('user_id');
```
To list users who have been blocked from seeing the story
```sh
echo $ig->people->getBlockedStoryList();
```
To hide or show your friend s stories
```sh
echo $ig->people->muteFriendStory('user_id');
echo $ig->people->unmuteFriendStory('user_id');
```
Mesaj (Direct) İşlemleri
List recent messages
```sh
echo $ig->direct->getInbox();
```
To get message details
```sh
echo $ig->direct->getThread('340282366841710300949128217702782372541');
```
To set up a message group
```sh
$userIds =['user_id', 'user_id2']; 
$groupTitle = 'Fatih SAYIN Group';
echo $ig->direct->createGroupThread($userIds, $groupTitle);
```
To add a new user to the group
```sh
echo $ig->direct->addUsersToThread('thread_id', ['user_id']);
```
To send a message to the group
```
$thread_id = 'grup idsi';
$msg = 'Test message!';
$ig->direct->sendText([
    'thread' => $thread_id
], $msg);
```
To send a photo to the group
```sh
$thread_id = 'group_id';
$photo = 'img/images.jpg';
$ig->direct->sendPhoto([
    'thread' => $thread_id
], $photo);
```
To send a disappearing photo to the group
```sh
$photo = 'img/photo.jpg';
$thread_id = 'group_id';
$ig->direct->sendDisappearingPhoto([
    'thread' => $thread_id
], $photo);
```
To mute or unmute the group
```sh
echo $ig->direct->muteThread('thread_id');
echo $ig->direct->unmuteThread('thread_id');
```
To leave the group
```sh
echo $ig->direct->leaveThread('thread_id');
```
To send a message to one or more users
```sh
$msg = 'Test Message';
$ig->direct->sendText([
    'users' => ['user_id']
], $msg);
```
To send a photo
```sh
$photo ='fotoğraf yolu';
$ig->direct->sendPhoto([
    'users' => ['user_id']
], $photo);
```
To send a lost photo
```sh
$photo = 'fotoğraf yolu';
$ig->direct->sendDisappearingPhoto([
    'users' => ['user_id']
], $photo);
```

To post hashtags
```sh
echo $ig->direct->sendHashtag(
    ['users' => ['user_id']],
    'tagdgtl'
);
```
To submit a location
```sh
echo $ig->direct->sendLocation(
    ['users' => ['user_id']],
    'lokasyon_id'
);
```
To submit a user profile
```sh
echo $ig->direct->sendProfile(
    ['users' => ['user_id']],
    'user_id' // mesajlaştığın kişiye göndereceğin kullanıcı
);
```
To delete a message you wrote
```sh
echo $ig->direct->deleteItem('thread_id', 'mesaj_id');
```
#Story
To add a photo to the story
```sh
$photo = __DIR__ . '/instagram.jpg';
$metadata = [
    'caption' => '#eskişehir bu story api ile gönderildi',
    'hashtags' => [
        [
            'tag_name'         => 'eskişehir',
            'x'                => 0.5,
            'y'                => 0.5,
            'width'            => 0.24305555,
            'height'           => 0.07347973,
            'rotation'         => 0.0,
            'is_sticker'       => false,
            'use_custom_title' => false
        ]
    ]
];

$photo = new \InstagramAPI\Media\Photo\InstagramPhoto($photo, ['targetFeed' => \InstagramAPI\Constants::FEED_STORY]);
echo $ig->story->uploadPhoto($photo->getFile(), $metadata);
```
To add a video to the story
```sh
\InstagramAPI\Media\Video\FFmpeg::$defaultBinary = '/usr/local/bin/ffmpeg';
\InstagramAPI\Utils::$ffprobeBin = '/usr/local/bin/ffprobe';

$video = __DIR__ . '/cplAZjZmjWRNtYya3pqZAfcnMsZ4P8K.mov';
$metadata = [
    'caption' => '#eskişehir bu story api ile gönderildi',
    'hashtags' => [
        [
            'tag_name'         => 'eskişehir',
            'x'                => 0.5,
            'y'                => 0.5,
            'width'            => 0.24305555,
            'height'           => 0.07347973,
            'rotation'         => 0.0,
            'is_sticker'       => false,
            'use_custom_title' => false
        ]
    ]
];

$video = new \InstagramAPI\Media\Video\InstagramVideo($video, ['targetFeed' => \InstagramAPI\Constants::FEED_STORY]);
echo $ig->story->uploadVideo($video->getFile(), $metadata);
```
To see the stories of your followers
```sh
echo $ig->story->getReelsTrayFeed();
```
To see the user's stories
```sh
echo $ig->story->getUserReelMediaFeed('user_id');
```
Join the live broadcast
```sh
echo $ig->story->getUserStoryFeed('1957285393');
```
To see the stories of multiple users
```sh
echo $ig->story->getReelsMediaFeed(['user_id', 'user_id2']);
```
To list archived stories
```sh
echo $ig->story->getArchivedStoriesFeed();
```
to see the details of the archived story
```sh
echo $ig->story->getReelsMediaFeed(['archiveDay:XXX']);
```
To list users who viewed the story
```sh
echo $ig->story->getStoryItemViewers('story_id');
```
View and change story sharing settings
```sh
echo $ig->story->getReelSettings();
```
To hide the story
```sh
echo $ig->people->blockMyStory('user_id');
echo $ig->people->unblockMyStory('user_id');
```
#Discover
To list the Discover feed
```sh
echo $ig->discover->getExploreFeed();
```
To search for users, tags and locations
```sh
echo $ig->discover->search('eskişehir');
```
To find suggested users, tags, and locations
```sh
echo $ig->discover->getSuggestedSearches('users');
echo $ig->discover->getSuggestedSearches('hashtags');
echo $ig->discover->getSuggestedSearches('places');
```
To list recent calls and clear call history
```sh
echo $ig->discover->getRecentSearches();
echo $ig->discover->clearSearchHistory()
```
#Hashtag
To view the label detail
```sh
echo $ig->hashtag->getInfo('eskişehir');
```
To view the stories for the tag
```sh
echo $ig->hashtag->getStory('eskişehir');
```
To search in tags
```sh
echo $ig->hashtag->search('eskişehir');
```
To follow/unfollow the tag
```sh
echo $ig->hashtag->follow('eskişehir');
echo $ig->hashtag->unfollow('eskişehir');
```
To find associated tags
```sh
echo $ig->hashtag->getRelated('php');
```
To list the shares belonging to the tag
```sh
echo $ig->hashtag->getFeed('codeigniter', \InstagramAPI\Signatures::generateUUID());
```
To list the tags the user is following
```sh
echo $ig->hashtag->getSelfFollowing(); //For the relevant user
echo $ig->hashtag->getFollowing('user_id'); //For different user
Tag tracking suggestions
```sh
echo $ig->hashtag->getFollowSuggestions();
```
