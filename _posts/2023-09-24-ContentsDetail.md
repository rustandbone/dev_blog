---
layout: post
title: "230924 일 멋쟁이사자처럼 React Project 콘텐츠 상세 페이지"
date: 2023-09-24 18:00:00 +0900
categories: likelion blog
published: true
---

# 콘텐츠 상세 페이지

- [콘텐츠 상세 페이지](#콘텐츠-상세-페이지)
  - [콘텐츠 상세 보기](#콘텐츠-상세-보기)

## 콘텐츠 상세 보기

- 유저 닉네임
- 사진
- 제목
- 내용
- 태그
- 커스텀 태그
- 만든 날짜

```jsx
<section className="shadow-content mt-5 mb-20 px-20 py-20 gap-5 flex flex-col items-center mx-[15%] min-h-full rounded-md s:mx-[5%] s:p-5">
  <h2 className="sr-only">{contentInfo.title}</h2>
  {/* 사진 */}
  <article>
    <img
      src={photo}
      alt={contentInfo.title}
      className="w-full h-full object-cover max-h-[100vh]"
    />
  </article>
  {/* 내용 */}
  <article className="w-full py-2 px-4 rounded-md border border-gray-500">
    <p className="pb-3 font-bold flex justify-between s:flex-col">
      {contentInfo.title}
      <span className="font-light text-slate-800 text-right">
        #{contentInfo.tag}
        <br />
        {contentInfo.customTag && `#${contentInfo.customTag}`}
      </span>
    </p>
    <div className="pb-4 flex justify-between">
      <p>{contentInfo.nickname}</p>
      <p>{contentInfo.created}</p>
    </div>
    <p className="w-full h-full resize-none whitespace-break-spaces">
      {contentInfo.content}
    </p>
  </article>
</section>;

{
  contentInfo.address !== null && contentInfo.location !== null && (
    <>
      <hr />
      <ShowMap address={contentInfo.address} location={contentInfo.location} />
    </>
  );
}
```

- db로부터 게시글 상세 정보를 가져옴. `contentInfoInit` 초기값에서 가져온 정보를 immer를 사용해서 수정함.

```jsx
const contentInfoInit = {
  title: "",
  content: "",
  tag: "",
  customTag: "",
  location: null,
  address: null,
  created: "",
  nickname: "",
};

const { id } = useParams();
const { user } = useAuthStore();

const writerRef = useRef(null);
const [photo, setPhoto] = useState();
const [comment, setComment] = useState([]);
const [contentInfo, setContentInfo] = useImmer(contentInfoInit);

useEffect(() => {
  async function getContent() {
    try {
      const jejuContent = await pb
        .collection("content")
        .getOne(
          id,
          { expand: "commentId,commentId.userId,userId" },
          { requestKey: "string" }
        );

      const {
        title,
        content,
        tag,
        customTag,
        expand,
        location,
        address,
        created,
      } = jejuContent;

      setPhoto(getPbImageURL(jejuContent, "photo"));

      setContentInfo((draft) => {
        draft.title = title;
        draft.content = content;
        draft.tag = tag;
        if (
          address !== "null" &&
          address !== "" &&
          location !== "null" &&
          location !== ""
        ) {
          draft.location = location;
          draft.address = address;
        }
        draft.customTag = customTag;
        draft.created = created.split(" ")[0];
        draft.nickname = expand.userId.nickname;
      });

      if (expand.commentId) setComment(expand.commentId);
      if (expand.userId) writerRef.current = expand.userId.username;
    } catch (error) {
      console.error(error);
    }
  }

  getContent();
}, [id, contentInfo, setContentInfo]);
```

- 작성자와 로그인 유저가 동일할 경우 삭제, 수정 버튼 보여줌

```jsx
{
  writerRef.current !== null && writerRef.current === user?.userId && (
    <div className="flex gap-2 ml-auto">
      <Link to={`/content/edit/${id}`}>
        <button
          className="border-2 border-blue text-darkblue py-2 px-4 rounded-lg font-semibold
        hover:text-white hover:bg-blue "
        >
          수정
        </button>
      </Link>
      <button
        className="bg-transparent text-red-500 border-2 border-red-500 py-2 px-4 rounded-lg
          font-semibold hover:border-2 hover:border-red-500 hover:bg-red-400 hover:text-white "
        onClick={handleDelete}
      >
        삭제
      </button>
    </div>
  );
}
```
