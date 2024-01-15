---
layout: post
title: "Jeju All in One 프로젝트 - 내 프로필 페이지"
date: 2023-09-24 18:00:00 +0900
categories: likelion blog React
published: true
---

# 내 프로필 페이지

- [내 프로필 페이지](#내-프로필-페이지)
  - [유저 정보](#유저-정보)
  - [작성한 게시글 모아서 보여주기](#작성한-게시글-모아서-보여주기)
  - [작성한 댓글 모아서 보여주기](#작성한-댓글-모아서-보여주기)

## 유저 정보

로그인한 유저 정보 가져와서 닉네임 보여줌

```jsx
useEffect(() => {
  async function getContent() {
    try {
      const jejuContent = await pb.collection("user").getOne(user.id);
      const { nickname } = jejuContent;
      setNickname(nickname);
    } catch (error) {
      console.error(error);
    }
  }

  getContent();
}, [user.id]);

<section className="pt-10 font-bold text-lg text-center">
  {nickname} 님 환영합니다.
</section>;
```

## 작성한 게시글 모아서 보여주기

- 3개 게시글 기본 보여주기
- 더보기 버튼 클릭 시 페이지당 9개 게시물 보여줌
- 페이지네이션 및 페이지 유지 가능
- 게시물 클릭 시 게시물로 이동 가능

```jsx
<ProfileContentSection
  showMore={showAllContent}
  setShowMore={setShowAllContent}
/>;

const { user } = useAuthStore();
const [searchParams, setSearchParams] = useSearchParams();
const [collection] = useState("content");
const [page, setPage] = useState(() => {
  const page = searchParams.get("page");
  return page ? Number(page) : 1;
});
const [perPage] = useState(9);
const [sort] = useState("-created");

useEffect(() => {
  const page = searchParams.get("page");
  if (!page) setSearchParams({ page: 1 });
  if (page >= 2) setShowMore(true);
}, [searchParams, setSearchParams, setShowMore, showMore]);

const { isLoading, data, isError, error } = useQuery({
  queryKey: ["contents", sort, user, page, perPage, collection],
  queryFn: () =>
    fetchProfileContents({
      sort,
      filter: `userId='${user.id}'`,
      page,
      perPage,
      collection,
    }),
  keepPreviousData: true,
});

return (
  <section className="mx-10 pt-10">
    <TitleButton
      title="나의 제주"
      link="#"
      count={data?.items?.length}
      section="게시글"
      onClick={() => setShowMore(!showMore)}
      showMore={showMore}
    />
    <hr />
    <ul className="my-10 w-11/12 mx-auto contentContainer">
      {data?.items?.length === 0 && <ContentItem />}
      {!showMore &&
        data?.items?.slice(0, 3).map((item) => {
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
      {showMore &&
        data?.items?.map((item) => {
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
    </ul>
    {(showMore || page >= 2) && (
      <PaginationButton
        totalPages={data.totalPages}
        page={page}
        setPage={setPage}
        setSearchParams={setSearchParams}
      />
    )}
  </section>
);
```

## 작성한 댓글 모아서 보여주기

- 5개 댓글 기본 보여주기
- 더보기 버튼 클릭 시 인피니트 쿼리로 가져온 댓글 보여줌
- 댓글 클릭 시 게시물로 이동 가능

```jsx
<ProfileCommentSection
  showMore={showAllComment}
  setShowMore={setShowAllComment}
/>;

const { user } = useAuthStore();
const [collection] = useState("comment");
const [sort] = useState("-created");

const {
  data,
  error,
  fetchNextPage,
  hasNextPage,
  isFetching,
  isFetchingNextPage,
  status,
} = useInfiniteQuery({
  queryKey: ["comment", sort, user, collection],
  queryFn: () =>
    fetchComments({
      sort,
      filter: `userId='${user.id}'`,
      collection,
    }),
  getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
});

return status === "loading" ? (
  <Spinner className="mx-auto" />
) : status === "error" ? (
  <div role="alert">{error.toString()}</div>
) : (
  <>
    <section className="mx-10 mb-12">
      <TitleButton
        title="나의 제주의 별"
        link="#"
        count={data.pages[0].items.length}
        section="댓글"
        onClick={() => setShowMore(!showMore)}
        showMore={showMore}
      />
      <hr />
      <ul className="w-11/12 mx-auto my-10">
        {showMore && data.pages[0].items.length === 0 && (
          <>
            <Link to="/content">
              <ProfileComment
                src="https://frontendschool6.github.io/finalize-react-6/jejuImage5.jpg"
                alt="제주 바다"
                date="2023-09-06"
                comment="제주를 향한 당신의 별을 우리에게 나누어주세요"
              />
            </Link>
          </>
        )}
        {!showMore &&
          data?.pages?.map((group, i) => {
            return (
              <React.Fragment key={i}>
                {group?.items?.slice(0, 5).map((project) => {
                  return (
                    <Link to={`/content/${project.contentId}`} key={project.id}>
                      <ProfileComment
                        src={getPbImageURL(project.expand.contentId, "photo")}
                        alt={project.title}
                        date={project.created.split(" ")[0]}
                        comment={project.comment}
                      />
                    </Link>
                  );
                })}
              </React.Fragment>
            );
          })}
        {showMore &&
          data?.pages?.map((group, i) => {
            return (
              <React.Fragment key={i}>
                {group?.items?.map((project) => {
                  return (
                    <Link to={`/content/${project.contentId}`} key={project.id}>
                      <ProfileComment
                        src={getPbImageURL(project.expand.contentId, "photo")}
                        alt={project.title}
                        date={project.created.split(" ")[0]}
                        comment={project.comment}
                      />
                    </Link>
                  );
                })}
              </React.Fragment>
            );
          })}
        {showMore && (
          <>
            <div>
              <button
                className="w-full py-2 font-semibold"
                onClick={() => fetchNextPage()}
                disabled={!hasNextPage || isFetchingNextPage}
              >
                {isFetchingNextPage
                  ? "로딩 중입니다."
                  : hasNextPage
                  ? "Load More"
                  : `- 댓글이 더 이상 없습니다. 우리의 제주에 당신의 별을 더욱 나눠주세요 -`}
              </button>
            </div>
            <div>
              {isFetching && !isFetchingNextPage ? (
                <p className="w-full text-center font-semibold">
                  - 추가된 댓글을 확인하고 있습니다 -
                </p>
              ) : null}
            </div>
          </>
        )}
      </ul>
    </section>
  </>
);
```
