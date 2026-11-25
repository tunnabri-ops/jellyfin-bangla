# Jellyfin বাংলা সেটআপ গাইড

Jellyfin ডকস থেকে যাচাই করা তথ্য দিয়েই এই গাইড লেখা। ডকস লিংকগুলো ২০২৬-১১-২৫ তারিখে যাচাই করা (সব HTTP 200)।

- ইনস্টলেশন: https://jellyfin.org/docs/general/installation/
- কুইক স্টার্ট: https://jellyfin.org/docs/general/quick-start
- সিনেমা নামকরণ: https://jellyfin.org/docs/general/server/media/movies
- সাবটাইটেল: https://jellyfin.org/docs/general/server/media/subtitles
- হার্ডওয়্যার ট্রান্সকোডিং: https://jellyfin.org/docs/general/administration/hardware-acceleration

## ১. চালু করা

```bash
mkdir -p config cache media/movies media/natok
docker compose up -d
```

ব্রাউজারে `http://localhost:8096` খুলুন। প্রথমবার সেটআপ উইজার্ড আসবে — অ্যাডমিন অ্যাকাউন্ট বানান।

## ২. লাইব্রেরি যোগ করা

Dashboard → Libraries → Add Media Library। লাইব্রেরির ধরন বাছুন (Movies / Shows), তারপর ফোল্ডার দিন:

- সিনেমা: `/media/movies`
- নাটক/সিরিজ: `/media/natok`

## ৩. ফোল্ডার নামকরণ (Jellyfin ডকস অনুযায়ী)

ডকস বলে: প্রতিটি সিনেমা আলাদা ফোল্ডারে রাখুন, ফোল্ডারের নাম এই ফরম্যাটে —

```
Movie Name (year)
```

আর ফোল্ডারের ভেতরের ভিডিও ফাইলের নাম ফোল্ডারের নামের মতোই হবে। উদাহরণ:

```
media/movies/
  Hawa (2022)/
    Hawa (2022).mp4
  Chorki Natok (2023)/
    Chorki Natok (2023).mkv
```

সিরিজ/নাটকের ক্ষেত্রেও একই নিয়ম — প্রতিটি শো আলাদা ফোল্ডার, ভেতরে সিজন ফোল্ডার।

## ৪. বাংলা সাবটাইটেল

বাইরের সাবটাইটেল ফাইল ভিডিও ফাইলের পাশে, একই নামে রাখুন — যেমন `Hawa (2022).bn.srt`। Jellyfin সেটা সাবটাইটেল হিসেবে চিনবে। সাবটাইটেল ফাইলের নামে ভাষা কোড (যেমন `bn`) দিলে Jellyfin ভাষা ঠিকমতো দেখায়। বিস্তারিত ডকসের সাবটাইটেল পাতায় আছে (উপরে লিংক)।

## ৫. হার্ডওয়্যার ট্রান্সকোডিং

যে ডিভাইসে ব্রাউজার/অ্যাপ চালাচ্ছেন, সেটা যদি ভিডিও ফরম্যাট সরাসরি চালাতে না পারে, Jellyfin ট্রান্সকোড করে। CPU দিয়ে ট্রান্সকোড চলে, তবে ভারী কাজের জন্য GPU (Intel Quick Sync / NVIDIA NVENC / VA-API) ব্যবহার করা যায় — সেটআপের পদ্ধতি ডকসের হার্ডওয়্যার ট্রান্সকোডিং পাতায় আছে (উপরে লিংক)। এই `docker-compose.yml`-এ GPU পাস-থ্রু দেওয়া নেই — GPU থাকলে ডকস দেখে `devices` যোগ করুন।

## ৬. দরকারি নোট

- পোর্ট 8096 হলো HTTP, 8920 হলো HTTPS। বাড়ির ভেতরে ব্যবহার করলে 8096-ই যথেষ্ট।
- `user: "1000:1000"` — আপনার ইউজারের UID/GID আলাদা হলে `id -u` / `id -g` দিয়ে দেখে বদলে নিন।
- `latest` ট্যাগের বদলে নির্দিষ্ট ভার্সন (যেমন `jellyfin/jellyfin:10.10.3`) পিন করতে চাইলে compose ফাইলে ট্যাগ বদলে নিন।
