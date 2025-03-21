# 󰏢 snake case to camelCase



## 문제 정의

`DB` <-> `백엔드` <-> `프론트`

통상 DB에서는 snake_case를 사용하고, 백/프론트에서는 camelCase를 쓰는 경우가 많다.
세 단위에서 데이터가 오갈 때 snake_case, camelCase 변환 전략이 정해져있으면 좋곘다.


## 해결: 어디에서 변환하는가?

_(특정 언어나 환경과 무관하게 어느 단위에서 작업할지가 중요하다.)_

| 변환 전략           | 장점                                               | 단점                                                    | 적합한 상황                                      |
|---------------------|----------------------------------------------------|---------------------------------------------------------|--------------------------------------------------|
| 백엔드에서 변환     | • 프론트엔드 단순화 / 일관된 API 응답              | • 백엔드 처리 부담 증가 / 클라이언트 유연성 감소        | • 단일 클라이언트 / 간단한 구조                  |
| 프론트엔드에서 변환 | • 백엔드 단순화 / 클라이언트 제어 용이             | • 프론트엔드 복잡성 증가 / 반복 변환 필요               | • 다수(종)의 클라이언트 / 클라이언트별 포맷 다양 |
| 중간 계층에서 변환  | • 백엔드와 프론트엔드 부담 분산 / 명확한 역할 분리 | • 중간 계층 유지보수 필요 / 간단한 구조에선 오히려 복잡 | • API 게이트웨이 / 대규모 시스템                 |


## 예시

### 유틸 함수 예시

- ```typescript
  // Utility function to convert snake_case keys to camelCase
  export function convertToCamelCase<T>(obj: any): T {
    if (obj === null || typeof obj !== 'object') {
      return obj;
    }

    if (obj instanceof Date) {
      return obj as T;
    }

    if (Array.isArray(obj)) {
      return obj.map((item) => convertToCamelCase(item)) as T;
    }

    return Object.entries(obj).reduce((acc, [key, value]) => {
      const camelCaseKey = key.replace(/_([a-z])/g, (_, letter) =>
        letter.toUpperCase(),
      );
      acc[camelCaseKey] = convertToCamelCase(value);
      return acc;
    }, {} as Record<string, unknown>) as T;
  }

  // Utility function to convert an array of database results to camelCase
  export function convertResultsToCamelCase<T>(results: any[]): T[] {
    if (!Array.isArray(results)) {
      throw new Error('Input must be an array.');
    }
    return results.map((result) => convertToCamelCase<T>(result));
  }
  ```

### 함수 활용 예시

- ```typescript
  ## File Path: src/controllers/communityPostController.ts, 141-193
  // 게시글 상세 조회
  export const getCommunityPostById = async (req: Request, res: Response) => {
    try {
      const { postId } = req.params;

      const [postResult]: [RowDataPacket[], any] = await database.query(
        `SELECT
          p.post_id,
          p.title,
          p.content,
          p.author_id,
          p.created_at,
          p.updated_at,
          s.status,
          s.reason
        FROM community_post_table p
        LEFT JOIN community_post_status_table s ON p.post_id = s.post_id
        WHERE p.post_id = ?`,
        [postId],
      );

      const [attachments]: [RowDataPacket[], any] = await database.query(
        'SELECT * FROM community_attachment_table WHERE post_id = ?',
        [postId],
      );

      const [comments]: [RowDataPacket[], any] = await database.query(
        `SELECT
          c.comment_id,
          c.content,
          c.author_id,
          c.created_at,
          u.name as author_name
        FROM community_comment_table c
        LEFT JOIN user_mapping_table u ON c.author_id = u.user_id
        WHERE c.post_id = ?`,
        [postId],
      );

      if ((postResult as any).length === 0) {
        res.status(404).json({ message: '게시글을 찾을 수 없습니다' });
      }

      res.json({
        ...convertToCamelCase(postResult),
        attachments: convertResultsToCamelCase(attachments),
        comments: convertResultsToCamelCase(comments),
      });
    } catch (error) {
      console.error('게시글 상세 조회 오류:', error);
      res.status(500).json({ message: '게시글 상세 조회 중 오류 발생' });
    }
  };
  ```

