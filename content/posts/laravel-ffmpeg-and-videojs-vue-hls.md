---
title: "laravel-ffmpeg と videojs で HLS の動画生成と再生をする"
date: 2022-09-20T03:29:08+09:00
---

## laravel-ffmpeg のインストール
公式： https://github.com/protonemedia/laravel-Ffmpeg

```sh
composer require php-ffmpeg/php-Ffmpeg pbmedia/laravel-ffmpeg
```

### Docker コンテナ内に ffmpeg をインストール
```sh
apt-get update
apt-get install ffmpeg
```

### 公式に従って、コンフィグの設定
```php
// config/app.php

'providers' => [
    ...
    ProtoneMedia\LaravelFFMpeg\Support\ServiceProvider::class,
    ...
];

'aliases' => [
    ...
    'FFMpeg' => ProtoneMedia\LaravelFFMpeg\Support\FFMpeg::class
    ...
];
```

```sh
php artisan vendor:publish --provider="ProtoneMedia\LaravelFFMpeg\Support\ServiceProvider"
```

#### ffmpeg のパスを確認して、コンフィグに設定
```sh
$ which ffmpeg
/usr/bin/ffmpeg
```

```php
<?php

return [
    'ffmpeg' => [
        'binaries' => env('FFMPEG_BINARIES', '/usr/bin/ffmpeg'),

        'threads' => 12,   // set to false to disable the default 'threads' filter
    ],

    'ffprobe' => [
        'binaries' => env('FFPROBE_BINARIES', '/usr/bin/ffprobe'),
    ],

    'timeout' => 3600,

    'log_channel' => env('LOG_CHANNEL', 'daily'),   // set to false to completely disable logging

    'temporary_files_root' => env('FFMPEG_TEMPORARY_FILES_ROOT', sys_get_temp_dir()),

    'temporary_files_encrypted_hls' => env('FFMPEG_TEMPORARY_ENCRYPTED_HLS', env('FFMPEG_TEMPORARY_FILES_ROOT', sys_get_temp_dir())),
];
```

### hls 用のファイルにコンバートするコマンド作成
```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use FFMpeg\Format\Video\X264;

class ExportHLSVideo extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'video:export-hls';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = '動画のHLSエンコーディング';

    /**
     * Execute the console command.
     *
     * @return int
     */
    public function handle()
    {
        $lowBitrate = (new X264)->setKiloBitrate(250);
        $highBitrate = (new X264)->setKiloBitrate(1000);

        $this->info('Start Converting.');

        \FFMpeg::fromDisk('video')
            ->open('sample.mp4')
            ->exportForHLS()
            ->addFormat($lowBitrate)
            ->addFormat($highBitrate)
            ->onProgress(fn ($progress) => $this->info("Progress {$progress}%"))
            ->toDisk('public')
            ->save('video/sample.m3u8');

        $this->info('Done!');
    }
}
```

### コマンド実行して、ファイルが生成されていることを確認
```sh
$ php artisan video:export-hls
Start Converting.
Progress 3%
Progress 5%
...
Progress 97%
Progress 98%
Progress 99%
Progress 100%
Done!
Task completed in 2m3.093s
```

```sh
# storage/app/public/video/

sample.m3u8
sample_0_250.m3u8
sample_0_250_00000.ts
sample_0_250_00001.ts
sample_0_250_00002.ts
sample_0_250_00003.ts
sample_1_1000.m3u8
sample_1_1000_00000.ts
sample_1_1000_00001.ts
sample_1_1000_00002.ts
sample_1_1000_00003.ts
```

## videojs のインストール
公式： https://github.com/surmon-china/videojs-player#usage-vue

```sh
npm install video.js @videojs-player/vue --save
```

### create 時に VueVideoPlayer を use する
```js
import VueVideoPlayer from '@videojs-player/vue'
import 'video.js/dist/video-js.css'

createInertiaApp({
  ...
  setup({ el, app, props, plugin }) {
    return createApp({ render: () => h(app, props) })
      ...
      .use(VueVideoPlayer)
      .mount(el);
  },
});
```

### VideoPlayer のコンポーネントを使用する
```vue
<script setup>
import BreezeAuthenticatedLayout from '@/Layouts/Authenticated.vue';
import { Head } from '@inertiajs/inertia-vue3';
import { VideoPlayer } from '@videojs-player/vue'
import 'video.js/dist/video-js.css'
</script>

<template>
  <Head title="Dashboard" />

  <BreezeAuthenticatedLayout>
    <template #header>
      <h2 class="font-semibold text-xl text-gray-800 leading-tight">
        Dashboard
      </h2>
    </template>

    <div class="py-12">
      <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
          <div class="p-6 bg-white border-b border-gray-200">
            <VideoPlayer
              src="storage/video/sample.m3u8"
              poster="/your-path/poster.jpg"
              type="application/x-mpegURL"
              controls
              :loop="true"
              :volume="0.6"
              />
          </div>
        </div>
      </div>
    </div>
  </BreezeAuthenticatedLayout>
</template>
```

