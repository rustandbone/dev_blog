---
layout: post
title: "Jeju All in One_React 프로젝트 기술서"
date: 2024-02-01 17:00:00 +0900
categories: blog React Project
published: true
---

## Jeju All in One_React 프로젝트 기술서

- [Jeju All in One\_React 프로젝트 기술서](#jeju-all-in-one_react-프로젝트-기술서)
  - [기간](#기간)
  - [링크](#링크)
  - [프로젝트 소개](#프로젝트-소개)
  - [조장 업무](#조장-업무)
  - [담당 작업](#담당-작업)

### 기간

2023년 9월 4일 ~ 9월 25일

### 링크

[Github][]  
[페이지별 작업 소개][]  
[배포 주소][]

### 프로젝트 소개

[멋쟁이사자처럼 프론트엔드 스쿨 6기]에서 진행한 React 프로젝트입니다. 제주도 감성 SNS로, 본인만이 알고 있는 제주도의 무엇을 ‘별’로 칭하여 사람들에게 별을 나눠주는 플랫폼입니다. 제주도 교통, 날씨 정보도 제공합니다.

### 조장 업무

이 프로젝트에서 조장으로서 팀원들의 순조로운 출발을 위해 추후에 배포할 때 이미지 최적화까지 고려한 초기 환경 설정을 도맡아 했습니다. 4명 모두 프론트엔드 개발자 지망생들이었기에, 함께 피그마로 작업할 페이지를 정하며 시안을 만들었습니다. 그 이후에는 페이지를 나눠서 마크업과 css만 리액트로 UI만 우선 분담하여 만들었습니다. 완료된 후에는 페이지별로 기능 사항 구현할 것을 함께 문서로 총정리하고, 페이지별로 다시 담당을 나누어서 담당 기능을 구현했습니다.  
팀으로서, 매일 오전과 오후에 한 차례씩 스크럼을 진행했습니다. 각자의 진행 상황을 공유하며 커밋과 머지를 주기적으로 해서 공통의 작업물과 총 진행 상황을 상시 볼 수 있도록 했습니다. 저는 팀원들의 오류나 문제 발생 시 해결하는 데 주로 도움을 주었습니다. 또한, 스크럼에서 관찰되는, 사용자 측면에서 개선점을 추가로 말해주어 디테일한 부분까지 챙기며 작업할 수 있도록 하였습니다. 초기 시안의 디자인이나 초기 기획에서의 사용성에서 아쉬운 측면은 수시로 얘기하며 개선되도록 했습니다. 제가 맡았던 작업은 온전히 수행했으며, 다른 팀원의 작업량을 고려해서 그 팀원의 작업량까지 추가로 이양 받아서 작업했습니다.  
스크럼을 매일 진행하면서 부지런히 작업하며 프로젝트 구현 목표치는 조기 달성했습니다. 그럼에도 프로젝트의 완성도를 높이기 위해 끝까지 노력했습니다. 버그 및 아쉬운 부분은 꾸준히 찾아내어 수정, 보완 작업을 끝까지 이어갔습니다. 전체적으로 빠르게 목표를 달성했기에, 홈페이지의 추가 기능을 더해 다음 목표 세워주고 도달을 독려했습니다.

### 담당 작업

저는 SNS로서 핵심적인 기능인 콘텐츠 목록과 등록, 상세 페이지 구현을 담당했습니다. 그리고 다른 팀원 작업량을 고려해 프로필 페이지도 이양받아 작업했습니다.  
콘텐츠 등록 페이지에서는 사진, 제목, 내용, 태그와 커스텀 태그, 지도(장소, 주소)를 등록할 수 있습니다. 태그는 지역별로 선택할 수 있고, 본인의 콘텐츠의 특징도 개성 있게 잘 나타낼 수 있는 커스텀 태그도 만들 수 있도록 했습니다. 또한 위치가 중요하기에 카카오 지도 API를 사용해서 장소를 검색해서 장소와 주소를 등록할 수 있도록 했습니다.  
콘텐츠 목록 페이지에서는 태그별로 정렬하는 기능, 다시 모아보는 기능이 있습니다. 최신순으로 볼 수 있도록 했고, 페이지네이션을 했고, 목록 페이지에서 상세 페이지로 이동했다가 다시 목록 페이지로 돌아올 때도 페이지가 유지되도록 했습니다. 목록 페이지는 사진이 특히 많아 렌더링 속도에 아쉬움이 있을 수 있어, TanStack Query를 사용해 한 번 가져온 데이터는 변경되지 않는 이상 일정 시간 동안 다시 데이터를 가져오지 않도록 해서 사용자성을 높였습니다.  
상세 페이지에서는 작성 유저의 닉네임, 사진, 제목, 내용, 태그, 커스텀 태그, 만든 날짜를 볼 수 있도록 했고, 댓글 또한 달 수 있습니다. 작성자와 로그인 유저가 동일한 경우에는 삭제, 수정 버튼을 보여줍니다.  
내 프로필 페이지에서는 유저의 닉네임과 작성한 게시글, 작성한 댓글을 모아서 보여줍니다. 작성 게시글과 댓글 역시 페이지네이션을 했습니다. 게시글의 경우는 페이지당 최대 9개의 게시물이 보이도록 했고, 댓글은 인피니티 쿼리를 사용해 스크롤을 내리면 댓글을 계속 불러오도록 했습니다. 게시글과 댓글을 클릭하면 상세 페이지로 이동합니다. 게시물이나 댓글이 한 개도 없는 경우 기본적으로 보이는 게시물, 댓글을 만들어 작성을 독려했습니다.  
사이트는 우선은 PC 웹 버전에 맞춰 목표를 설정하고 진행했습니다. 하지만 SNS 특성상 모바일로도 많이 쓰일 것을 생각하며 모바일 뷰도 염두를 두고 작업을 했습니다. 덕분에, 기존 목표 달성 후에는 모바일 뷰 최적화까지 할 수 있었습니다. 반응형으로 어떤 기기에서든 사이트를 이용하는데 문제없도록 구현했습니다.  
프로젝트 기간 후에도, 다크모드 같은 개인적으로 하고 싶었던 기능과 닉네임 수정 같은 편의 기능을 추가 구현했습니다.  
프로젝트를 진행하면서 특히 신경썼던 부분은 내가 사용자라면 어떨까 지속적으로 대입해보는 것이었습니다. 불편을 최소화하려고 했으며, 무의미한 페이지가 없이 온전히 작동되도록 특히 신경썼습니다.

[Github]: https://github.com/FRONTENDSCHOOL6/finalize-react-6
[배포 주소]: https://frontendschool6.github.io/finalize-react-6/
[페이지별 작업 소개]: https://github.com/FRONTENDSCHOOL6/finalize-react-6/wiki/%5B%ED%8E%98%EC%9D%B4%EC%A7%80%EB%B3%84-%EC%9E%91%EC%97%85-%EC%86%8C%EA%B0%9C%5D