---
layout: post
title: "Jeju All in One 프로젝트 - 콘텐츠 등록 페이지"
date: 2023-09-23 18:00:00 +0900
categories: likelion blog React
published: true
---

# 콘텐츠 등록 페이지

- [콘텐츠 등록 페이지](#콘텐츠-등록-페이지)
  - [콘텐츠 등록하기](#콘텐츠-등록하기)

## 콘텐츠 등록하기

- 사진
- 제목
- 내용
- 태그
- 커스텀 태그
- 장소명 + 주소

```jsx
const titleRef = useRef(null);
const contentRef = useRef(null);
const customTagRef = useRef(null);
const tagRef = useRef(null);
const photoRef = useRef(null);
const placeNameRef = useRef(null);
const placeAddressRef = useRef(null);

const handleCreate = async (e) => {
  e.preventDefault();

  const titleValue = titleRef.current.value;
  const contentValue = contentRef.current.value;
  const photoValue = photoRef.current.files;
  const tagValue = tagRef.current.value;
  const customTagValue = customTagRef.current.value;

  if (!tagValue) {
    toast("태그를 선택해주세요.", {
      position: "top-center",
      icon: "🚨",
      ariaProps: {
        role: "alert",
        "aria-live": "polite",
      },
    });
    return;
  }

  if (!photoValue[0]) {
    toast("사진을 등록해주세요.", {
      position: "top-center",
      icon: "🚨",
      ariaProps: {
        role: "alert",
        "aria-live": "polite",
      },
    });
    return;
  }

  const formData = new FormData();

  formData.append("title", titleValue);
  formData.append("content", contentValue);
  formData.append("location", placeNameRef.current);
  formData.append("address", placeAddressRef.current);
  formData.append("tag", tagValue);
  formData.append("customTag", customTagValue);
  formData.set("userId", pb.authStore.model.id);
  if (photoValue) {
    formData.append("photo", photoValue[0]);
  }

  try {
    const contentData = await pb.collection("content").create(formData);
    if (contentData) {
      await pb
        .collection("user")
        .update(user.id, { "content+": contentData.id });
    }
    navigate("/content/list");
    toast("등록되었습니다.", {
      position: "top-center",
      icon: "⭐",
      ariaProps: {
        role: "status",
        "aria-live": "polite",
      },
    });
  } catch (error) {
    console.error(error);
    alert("오류가 발생해 콘텐츠 등록에 실패했습니다. 다시 시도해주세요.");
  }
};
```

- 커스텀 태그
  - 빈 칸은 \_로 변경

```jsx
const handleNoSpace = () => {
  customTagRef.current.value = customTagRef.current.value.replace(
    /(\s+)/g,
    "_"
  );
};
```

- 사진 이미지 보여주기
  - 가로, 세로 수용 가능

```jsx
const [fileImages, setFileImages] = useState(null);

const handleFileUpload = (e) => {
  const { files } = e.target;
  const fileImages = Array.from(files).map((file) => ({
    image: URL.createObjectURL(file),
    label: file.name,
  }));
  setFileImages(fileImages);
};

<div className="flex gap-2 overflow-x-auto p-2 w-full min-w-[350px] h-full bg-slate-100 s:min-h-[350px] s:max-h-[1000px]">
  {!fileImages && (
    <img
      src={photo}
      alt="photo"
      className="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2"
    />
  )}
  {fileImages?.map((file) => {
    return (
      <img
        key={file.label}
        src={file.image}
        alt={file.label}
        className="m-auto bg-slate-100 max-h-[660px]"
      />
    );
  })}
</div>;
```
