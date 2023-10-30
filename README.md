# ytdl

Go library for downloading YouTube videos

[![Documentation](https://godoc.org/github.com/rylio/ytdl?status.svg)](http://godoc.org/github.com/rylio/ytdl)
[![Build Status](https://github.com/rylio/ytdl/workflows/build/badge.svg?branch=master)](https://github.com/rylio/ytdl/actions)
[![Codecov](https://codecov.io/gh/rylio/ytdl/branch/master/graph/badge.svg)](https://codecov.io/gh/rylio/ytdl)
[![Go ReportCard](https://img.shields.io/badge/go%20report-A+-brightgreen.svg?style=flat)](http://goreportcard.com/report/rylio/ytdl)


## Example

```go
package main

import (
	"context"
	"os"

	"github.com/Andreychik32/ytdl"
)

func main() {
	ctx := context.Background()
	client := ytdl.DefaultClient

	videoInfo, err := client.GetVideoInfo(ctx, "https://www.youtube.com/watch?v=WkVvG4QTO9M")
	if err != nil {
		panic(err)
	}

	file, err := os.Create(videoInfo.Title + ".mp4")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	err = client.Download(ctx, videoInfo, videoInfo.Formats[0], file)
	if err != nil {
		panic(err)
	}
}
```

## ytdl CLI

- To install: `go get -u github.com/rylio/ytdl/...`

- Or use Docker image `docker pull brucewangno1/ytdl:1.0`

### Usage

- `ytdl [global options] [youtube url or video id]`
- Or using Docker: `docker run -it --rm -v /directory/you/want/to/save/the/download/:/ytdl/ brucewangno1/ytdl:1.0 [global options] "[youtube url or video id]"`

### Options

- `--help, -h` - show help
- `--filter, -f` - Filter out formats
  - Syntax: `-f key:value1,value2,...,valueN`
    - Shortcuts for best/worst(e.g. `-f best`)
      - `best`/`worst` - best/worst video and audio
      - `best-video`/`worst-video` - best/worst video
      - `best-fps`/`worst-fps` - best/worst video with fps as the first priority
      - `best-audio`/`worst-audio` - best/worst audio
  - To exclude:  -f !key:value1,...
  - Available keys (See format.go for available values):
    - `ext` - extension of video
    - `res` - resolution of video
    - `videnc` - video encoding
    - `audenc` - audio encoding
    - `prof` - youtube video profile
    - `audbr` - audio bitrate
  - Default filters
    - `ext:mp4`
    - `!videnc:`
    - `!audenc:`
    - `best`
- `--output, -o` - Output to specific path
  - Supports templates, ex: {{.Title}}.{{.Ext}}
  - Defaults to `{{.Title}}.{{.Ext}}`
  - Supported template variables are Title, Ext, DatePublished, Resolution
  - Pass - to output to stdout, former stdout output is redirected to stderr
- `--info, -i` - Just gets video info, outputs to stdout
- `--silent, -s` - Disables all output, except for fatal errors
- `--debug, -d` - Output debug logs
- `--append, -a` - append to output file, instead of truncating
- `--range, -r` - specify a range of bytes, placed in http range header, ex: 0-100
- `--download-url, -u` - just print download url to, don't do anything else
- `--version, -v` - print out ytdl cli version
- `--start-offset` - offset the beginning of the video by a duration of time(e.g. 20s or 1m)
- `--download-option, -p` - Print video and audio download options and accept input interactively


### Examples

Download content based on itag

```bash
ytdl -f itag:22 'https://www.youtube.com/watch?v=9bZkp7q19f0'
```

Download content with the best fps

```bash
ytdl -f best-fps 'https://www.youtube.com/watch?v=9bZkp7q19f0'
```

Get all download formats (Requires [jq](https://github.com/stedolan/jq) to be installed)

```bash
./ytdl -j 'http://youtube.com/watch?v=9bZkp7q19f0' | jq ".formats"
```

Extract title of the video (Requires [jq](https://github.com/stedolan/jq) to be installed)

```bash
ytdl -j 'http://youtube.com/watch?v=9bZkp7q19f0' | jq ".title"
```

Print download url without downloading the content

```bash
ytdl -f itag:22 --download-url 'https://www.youtube.com/watch?v=9bZkp7q19f0'
```

Print video and audio download options and accept input interactively

```bash
ytdl -p 'https://www.youtube.com/watch?v=9bZkp7q19f0'
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## License

ytdl is released under the MIT License, see LICENSE for more details.
