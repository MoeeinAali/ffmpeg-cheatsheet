
## کامند برای فشرده کردن ویدیوها
این کامند مناسب فشرده کردن و ارسال داخل تلگرام است. اگه تو پخش کردنش با پلیرهای ویندوز به مشکل خوردید جلوتر گفته میشه چه کنید. اما این روش **بیشترین کاهش حجم** را دارد.
```
ffmpeg -i input.webm -r 15 -vf "scale=-2:720" -c:v libx265 -crf 28 -c:a libopus -b:a 32K -strict experimental -max_muxing_queue_size 1024 output.mp4
```

اگه اسلاید داره نمایش داده میشه و ویدیو تحرک کمی داره، عدد 15 را می توانید کمتر هم بکنید.
اگه ویدیو تحرک زیادی دارد (مثلا کنفرانسی هست که همگی وبکم زدن)، عدد 15 را بیشتر کنید. مثلا 30.
برای تنظیم طول و عرض ویدیو، باید scale را تغییر دهید. در حالت فعلی ارتفاع 720 است و عرض با حفظ نسبت اولیه ویدیو به نزدیکترین عدد زوج تبدیل می شود. اگه بخواهید ارتفاع هم دستی ست کنید ممکن است نسبت ابعاد ویدیو بهم بخورد. اگر از -1 استفاده کنید نزدیکترین عدد فرد انتخاب میشود که توصیه نمیشود. چون برخی کدک ها الزامی است که عدد زوج باشند.

عدد crf نشان دهنده ی `Constant Rate Factor` است که عددی بین 0 تا 51 است. هرچه این عدد بالاتر برود کیفیت پایین تر میاید و حجم کمتر میشود. مقدار 23 پیش فرض است.


اگر ویدیوها را با این روش فشرده کنید احتمالا برای آپلود آن در اینستاگرام و یوتیوب و... به مشکل برخواهید خورد. دلیل آن نوع encoding صدا است. همچنین وضعیت فعلی دلیل پلی نشدن ویدیو در برخی پلیرهای ویندوز است.

پلیرهای kmplayer و vlc مشکلی با پلی کردن ویدیوها ندارند. اما پلیر پیشفرض ویندوز ویدیوهایی که با این روش encode شده اند را پلی نمیکند.


برای پلی شدن ویدیو در پلیر ویندوز و همچنین نداشتن مشکل برای آپلود آن در یوتیوب و آپارات و...، می توانید از این کامند استفاده کنید:

```
ffmpeg -i input.webm -r 15 -vf "scale=-2:720" -c:v libx265 -crf 28 -c:a aac -b:a 32K -strict experimental -max_muxing_queue_size 1024 output.mp4
```
در این کامند، به جای استفاده از libopus  ما از aac استفاده میکنیم تا انکودینگ صدا به مشکل نخورد.

اگر می خواهید این ویدیو را روی html استفاده کنید، ممکن است روی برخی browserها پلی نشود. برای این کار باید کدک H.265/HEVC که یک کدک نسل جدید است را به ورژن قبلی خود که H264 است برگردانیم. البته کدک جدیدتر کاهش حجم بهتری دارد اما روی برخی browserها اجرا نمیشود.

```
ffmpeg -i input.webm -r 15 -vf "scale=-2:720" -c:v libx264 -crf 28 -c:a aac -b:a 32K -strict experimental -max_muxing_queue_size 1024 output.mp4
```
این ویدیو هم از aac استفاده میکند هم از h264. پس به هیچ عنوان نباید روی هیچ browser و یا شبکه اجتماعی ای به مشکل بخورد.
برای کاهش حجم بیشتر ویدیوها با این فرمت، مجبوریم کیفیت را کاهش دهیم و عدد crf را بالاتر ببریم. 

در کامندهای فوق میزان بیت‌ریت  صدا بسیار کم است و حجم در کمترین حالت ممکن است. در حال حاضر -b:a 32K یعنی صدای خروجی 32 کیلوبیت بر ثانیه است. برای افزایش کیفیت صدا میتوان آن را روی 128 یا 256 قرار داد که توصیه نمیشود و همین 32 اوکی هست.


## کامند برای جدا کردن صدا و تصویر

اگر بخواهیم صدای یک ویدیو را جدا کنیم و فقط تصویر آن را داشته باشیم، باید از کامند زیر استفاده کنیم:

```
ffmpeg -i input.mp4 -an -vcodec copy output_video.mp4
```

اگر بخواهیم فقط صدا را خروجی بگیریم می توانیم از این کامند استفاده کنیم:
```
ffmpeg -i input.mp4 -vn -acodec copy output_audio.aac
```

کارهای پردازشی و کاهش حجمی روی هیچ کدام از خروجی های فوق اعمال نمیشود. این کامندها صرفا جدا کننده هستند. 

## حذف نویز صدا

برای حذف نویز به صورت دستی از یک ویس میتوان از این دستور استفاده کرد:
```
ffmpeg -i input.aac -af "highpass=f=200, lowpass=f=3000" -c:a libopus -b:a 96K out.ogg
```

برای استفاده از فیلترهای آماده میتوان از دستور زیر استفاده کرد که توصیه میشود:
```
ffmpeg -i input_audio.mp4 -af afftdn output_audio_clean.mp4
```

اگر میخواهید به صورت دستی میزان حذف نویز را تنظیم کنید:

```
ffmpeg -i input_audio.mp4 -af "afftdn=nf=-25" output_audio_clean.mp4
```

مقدار nf=-25: سطح حذف نویز را تنظیم می‌کند. هر چه مقدار آن منفی‌تر باشد، شدت حذف نویز بیشتر استi 


## کات کردن ویدیو

برای کات کردن ویدیو از زمان مشخص x تا زمان مشخص y، از دستور زیر استفاده کنید:
```
ffmpeg -i input.mp4 -ss 00:01:30 -to 00:02:00 -c:v copy -c:a copy output.mp4
```

برای کات کردن ویدیو از زمان مشخص x تا انتهای ویدیو، از دستور زیر استفاده کنید:
```
ffmpeg -i input.mp4 -ss 00:01:30 -c:v copy -c:a copy output.mp4
```
برای کات کردن ویدیو از ابتدای آن تا زمان مشخص x، از دستور زیر استفاده کنید:
```
ffmpeg -i input.mp4 -to 00:02:00 -c:v copy -c:a copy output.mp4
```

کامندهای فوق هیچ پردازشی روی ویدیو و صدا انجام نمیدهند و فقط عمل cut را انجام میدهند. برای فشرده کردن میتوان از کامندهای قبلی استفاده کرد.


## ترکیب صدا و تصویر مجزا
برای ترکیب یک ویدیو و یک فایل صوتی به‌گونه‌ای که صدای ویدیو حذف شود و به‌جای آن صدای جدید از فایل صوتی وارد شود، می‌توانید از دستور زیر استفاده کرد:
```
ffmpeg -i input_video.mp4 -i input_audio.mp3 -c:v copy -c:a aac -b:a 192K -shortest output.mp4
```

می توانید بیت‌ریت و کدک را به صورت دلخواه تغییر دهید.

فلگ -shortest: این گزینه باعث می‌شود که خروجی فقط به اندازه کوتاه‌ترین ورودی (ویدیو یا صدا) باشد. در نتیجه، اگر یکی از فایل‌ها طولانی‌تر باشد، خروجی متوقف می‌شود و صدا یا ویدیو به پایان می‌رسد.


## چسباندن دو ویدیو به هم
اگر ابعاد دو ویدیو متفاوت باشد، باید یکی از ویدیوها را به اندازه ویدیو دیگر تغییر  بدهیم. 
```
ffmpeg -i video1.mp4 -i video2.mp4 -filter_complex "[0:v][1:v]concat=n=2:v=1:a=1[outv][outa]" -map "[outv]" -map "[outa]" output.mp4
```

## انکود کردن برای آپلود در اسپات پلیر :))

باید از این دستور استفاده کنیم:
```
ffmpeg -i input.webm -r 15 -vf "scale=-2:720" -c:v libx264 -crf 28 -c:a aac -map_metadata -1 -metadata comment=spot1@2000 -b:a 32K -strict experimental -max_muxing_queue_size 1024 output.mp4
```

انکودینگ و متادیتاها را به هیچ عنوان تغییر ندهید. چون Spotplayer فقط همین تنظیمات را قبول میکند.
می توانید مواردی مثل بیت‌ریت را تغییر دهید، اما انکودینگ و فرمت ها را تغییر ندهید.

## تغییر ابعاد یک ویدیو

اگر بخواهیم ابعاد یک ویدیو را دقیقا برابر و یکسان با یک ویدیو دیگر کنیم، از این دستور استفاده میکنیم:
```
ffmpeg -i input_video.mp4 -i reference_video.mp4 -filter_complex "[0:v][1:v]scale2ref=iw:ih[vid1][vid2];[vid1][vid2]overlay=0:0" -c:a copy output_video.mp4
```