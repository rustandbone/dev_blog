---
layout: post
title: "230829 화 멋쟁이사자처럼 React Project 콘텐츠 목록 페이지"
date: 2023-09-22 18:00:00 +0900
categories: likelion blog
published: true
---

# 콘텐츠 목록 페이지

- [콘텐츠 목록 페이지](#콘텐츠-목록-페이지)
  - [콘텐츠 목록 가져오기](#콘텐츠-목록-가져오기)
  - [페이지네이션](#페이지네이션)
  - [정렬](#정렬)

## 콘텐츠 목록 가져오기

- TanStack Query를 사용해 서버에서 데이터를 역순 정렬 후 가져옴
- 페이지 당 9개의 게시글 보여줌

```jsx
const [sort] = useState("-created");
const [perPage] = useState(9);
const [tag, setTag] = useState("");

const { isLoading, data, isError, error } = useQuery({
  queryKey: ["contents", sort, tag, page, perPage],
  queryFn: () =>
    fetchContents({ sort, filter: `(tag='${tag}')`, page, perPage }),
  keepPreviousData: true,
});
```

```jsx
//fetchContents
export async function fetchContents(options) {
  let queryParams = "";

  if (options.filter === `(tag='')`) {
    queryParams = `?sort=${options.sort}&page=${options.page}&perPage=${options.perPage}`;
  } else {
    queryParams = `?${Object.entries(options)
      .map(
        ([key, value]) =>
          `${encodeURIComponent(key)}=${encodeURIComponent(value)}`
      )
      .join("&")}`;
  }

  const response = await fetch(
    `${import.meta.env.VITE_PB_API}/collections/content/records${queryParams}`
  );
  return await response.json();
}
```

- 게시글 렌더링

```jsx
<section className="contentContainer p-1 bg-gray-100 w-11/12">
  {data.items.length === 0 && <ContentItem />}
  {data?.items?.map((item) => {
    return (
      <ContentItem
        key={item.id}
        content={item.id}
        title={item.title}
        count={item.commentId.length}
        customTag={item.customTag}
        src={getPbImageURL(item, "photo")}
      />
    );
  })}
</section>
```

## 페이지네이션

- 상세 페이지로 이동 후 돌어올 때 페이지 유지

```jsx
const [searchParams, setSearchParams] = useSearchParams();

const [page, setPage] = useState(() => {
  const page = searchParams.get("page");
  return page ? Number(page) : 1;
});

useEffect(() => {
  const page = searchParams.get("page");
  if (!page) setSearchParams({ page: 1 });
}, [searchParams, setSearchParams]);
```

- 이전 이후 페이지 이동 가능

```jsx
<PaginationButton
  totalPages={data.totalPages}
  page={page}
  setPage={setPage}
  setSearchParams={setSearchParams}
/>;

//PaginationButton
<section className="flex justify-center gap-5 my-10">
  <button
    onClick={() => {
      setPage((old) => Math.max(old - 1, 0));
      setSearchParams((searchParams) => {
        const page = searchParams.get("page");
        return { page: Number(page) - 1 };
      });
    }}
    disabled={page === 1}
    className="disabled:font-extralight font-bold"
  >
    <span className="sr-only">이전 페이지</span>
    &lt;
  </button>
  <span>
    {`${page}`}
    {totalPages !== 1 && totalPages !== 0 && ` / ${totalPages}`}
  </span>
  <button
    onClick={() => {
      setPage((old) => old + 1);
      setSearchParams((searchParams) => {
        const page = searchParams.get("page");
        return { page: Number(page) + 1 };
      });
    }}
    disabled={page === totalPages || totalPages === 0}
    className="disabled:font-extralight font-bold"
  >
    <span className="sr-only">이후 페이지</span>
    &gt;
  </button>
</section>;
```

## 정렬

- 모아보기
- 태그 선택 시 태그별로 정렬

```jsx
<section className="w-11/12 text-right mb-5">
  <button onClick={() => setTag("")} type="button" className="mr-2">
    모아보기
  </button>
  <select name="태그" id="tagSelect" onChange={handleTagSelect} value={tag}>
    <option>- 태그 -</option>
    {colourOptions.map((item) => (
      <option value={item.value} key={item.value}>
        {item.value}
      </option>
    ))}
  </select>
</section>
```
