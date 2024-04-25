---
title: 'gatsby로 블로그 만들기 1탄: gatsby 이해하기'
birth: '2023-05-29'
modified: '2023-05-29'
slug: '2023/05/make-gatsby-blog-1st'
---

# Questions

- why SSG?
- why Gatsby?
- Gatsby basic

# Answers

## why SSG?

배포하려는 github page에서 static site를 지원하기 때문이다.

## why Gatsby?

가장 먼저 눈에 띈 것은 Jekyll이었다. 그러나 다음과 같은 차이로 Gatsby를 선택했다.

### why not Jekyll

- Ruby language: 좀 배워야 블로그 세부 수정이 가능하다.

### why Gatsby

- React 기반
- plugin을 추가해 사용하는 방법이 쉽다.
- 향후 vimwiki를 연동해서 markup파일을 바로 포스팅 하는 방식으로 만들 생각인데, 관련 플러그인이 있는 듯 하다.

이 외에도 'Jekyll vs Gatsby'라고 검색하면 유수의 기술회사 블로그들이 `Jekyll to Gatsby 마이그레이션` 따위의 포스팅과 함께 마이그레이션을 진행해서 나도 따라해 본 면도 있다.

Next.js도 SSG를 지원하긴 하지만, 페이지나 그 내용들이 딱히 dynamic하지 않은 blog 사이트의 경우 Gatsby 정도가 심플한 것이 딱 알맞다고도 한다.

## Gatsby basic

### gatsby-cli

gatsby-cli 패키지를 설치하고, `gatsby new` 명령을 실행하면 user interactive한 프로그램이 실행된다. title, directory, typescript를 쓸 것인지 등등 선택하고 프로젝트를 생성한다.

### plugin

Gatsby측에서 직접 만든 plugin도 있고, 커뮤니티에서 만들어지는 것도 있다. npm package이다. [여기](https://www.gatsbyjs.com/plugins)에서 찾아보면 된다.

설치하고 나서, `root/gatsby-config.ts`의 `plugin` 속성에 plugin name(string) 또는 config object를 추가 해 주면 된다. 이와 관련된 내용은 플러그인 소개에 자세히 나와 있다.

### data-layer

<details>
  <summary>요약: 일반적인 데이터 사용 과정</summary>
  <div style="background-color: #E2EADD; padding: 10px">
  
1. Add a source plugin to add data into the GraphQL data layer.
2. Use GraphiQL to design a query that responds with the data you want from the data layer.
3. Add the query into your component.
    - Use page queries for page components.
    - Use useStaticQuery for “building block” components.
4. Use the data from the response in your component.
</div>
</details><br>

Gatsby에는 data-layer라는 것이 존재한다. build시에 graphQL 서버까지 자동으로 생성되고, 이 서버에 접근 할 수 있게 해 주는 것이다.

이 레이어 내부에 다양한 소스로부터 데이터가 저장되는데, 기본적으로 설정 된 gatsby-config.ts 파일이나 여러가지 외부 저장소, API, local file system등에서 받아온다. 이때 외부 시스템에서 데이터를 가져와 data-layer에 저장 해 두는 역할을 각종 플러그인이 해 준다. 필요한 것을 받아서 설치하자.

이후에 컴포넌트에서 graphQL query를 작성하여 data-layer에서 필요한 데이터를 갖다 쓴다. 주의 할 것은 한 파일에서 한번 쿼리를 날릴 수 있다.

> dev모드로 로컬에서 빌드 해 보면 'http://localhost:8000/\_\_\_graphql' 에서 query를 생성하고 테스트 해 볼 수 있는 인터페이스를 제공하기도 한다.<br><br>그러나 본격적으로 사용하기 위해서는 graphQL language server LSP와 함께 graphQL.config를 구성하여 [멋지게 사용](https://www.gatsbyjs.com/blog/how-to-use-gatsby-graphql-type-generation/)하자.

page component와 이른바 building-block component에서의 query 사용이 약간 다르니, 주의하여 사용하자.

<details>
  <summary>building-block component</summary>
  <div style="background-color: #E2EADD; padding: 10px">
  
```
import { graphql, useStaticQuery } from "gatsby"

const data = useStaticQuery(graphql`  query {
    site {
      siteMetadata {
        title
      }
    }
  }`)

```
</div>
</details>

<details>
  <summary>page component</summary>
  <div style="background-color: #E2EADD; padding: 10px">

page 컴포넌트 내부가 아니라 (동일한 파일의)외부 스코프에서 쿼리를 생성하고 export 해 준다.

[Gatsby public tutorial](https://www.gatsbyjs.com/docs/tutorial/getting-started/part-4/#task-use-a-page-query-to-pull-the-list-of-post-filenames-into-your-blog-page)에서 제공하는 다음 설명을 참고하자.

The process for making a query in a page component looks slightly different from useStaticQuery:

1. Import the graphql tag from the Gatsby package.
2. Export a variable that stores a templated string with the GraphQL query you want to run.
    - When your site gets built, Gatsby will run your page query and pass the resulting data into your page component as a prop called data.
    - Your page query needs to be defined outside of your page component. (With useStaticQuery, your query was defined inside your component.)
3. Use the data prop in your page component, as needed. You can use the JavaScript dot operator (.) to choose fields off of the data prop.

Here’s a small example to show what this process looks like in practice:

```

import \* as React from "react"

// Step 1: Import the graphql tag
import { graphql } from "gatsby"

const HomePage = ({ data }) => {
return (

<p>
{/_ Step 3: Use the data in your component_/}
{data.site.siteMetadata.description}
</p>
)
}

// Step 2: Export a page query
export const query = graphql`  query {
    site {
      siteMetadata {
        description
      }
    }
  }`

export default HomePage

```
</div>
</details>

### Transformer plugin)

transformer plugin의 역할은 이름 처럼 이미 data-layer에 존재하는 데이터(nodes)를 다른 형태로 바꾸는 것이다. 원본은 그대로 유지된다.

예를 들면 gatsby-plugin-mdx 같은 것이 있다.

### 동적으로 page 생성하기

`src/pages/` 디렉토리에 저장된 파일이 하나의 페이지가 된다. 즉 라우팅 된다. 이 점을 떠올리며 동적으로 페이지를 생성하는 방법을 알아 보자.

나는 `src/blogs/`에 저장 해 둔 .md 또는  .mdx 파일들을 각종 플러그인을 통해 data-layer에서 가져올 수 있다. 어찌됐든 이 데이터로부터 페이지를 생성하게 된다.

어떤 nodes로부터 페이지를 생성할지 정했다면, slug로 사용할 query field도 생각 해 둔다. 그리고 `src/pages/`에다가 다음과 같은 형식의 이름으로 페이지 컴포넌트를 생성한다. 일종의 템플릿이 되는 것이다.

>`{nodeType.field}.ts`

`{ }`(curly braces)가 중요하다. 이를 통해 동적으로 생성 될 페이지 컴포넌트임을 알리는 것이다.

예를들어 mdx nodes로부터 frontmatter의 slug field를 이용한다면 `{mdx.frontmatter__slug}.tsx` 따위를 쓸 수 있다. 이런 이름은 마음대로 짓는게 아니라 nodes타입과 필드 이름을 정확한 규칙에 따라 써 줘야한다.

### router path 생성하기

`src/pages/` 내부에 sub-directories 만들고 넣어준다.



### Pro tips

<details>
  <summary><code>gatsby clean</code> command</summary>
  <div style="background-color: #E2EADD; padding: 10px">

Pro Tip: Gatsby caches information about your site as it builds it, to make subsequent builds faster. But sometimes, when you make changes to your site, you’ll need to empty the cache for your changes to show up.

If you’re seeing unexpected behavior (like maybe your local development server isn’t picking up your new changes), you can run gatsby clean from the command line to delete the cache and start fresh on your next build.

Don’t have the Gatsby CLI globally installed? Try running npx gatsby clean instead.
  </div>
</details><br>

<details>
  <summary>page component props의 구성</summary>
  <div style="background-color: #E2EADD; padding: 10px">

  page query의 fields에 해당하는 key값들이 있다. 원하는 대로 갖다 쓰자.
```

Object {
...
pageContext: Object {
id: "11b3a825-30c5-551d-a713-dd748e7d554a"
frontmatter\_\_slug: "my-first-post"
}
...
}

```
</div>
</details><br>

__"graphQL의 query variable은 page query에서만 사용 가능하다."__

## __[learn more](https://www.gatsbyjs.com/docs/tutorial/getting-started/whats-next/)__

# Reference

https://www.gatsbyjs.com/docs/tutorial/getting-started/
```
