# 修改Url的SearchParams(useUpdateSearchParams.ts)

```ts
import { useSearchParams } from "next/navigation"
import { useCallback } from "react"


export const useUpdateSearchParams = () => {

    const searchParams = useSearchParams()

    const updateSearchParams = useCallback(
        (name: string, value: string) => {
          const params = new URLSearchParams(searchParams.toString())
          params.set(name, value)
     
          return params.toString()
        },
        [searchParams]
      )


    return { updateSearchParams }
} 
```
## Example
```ts
const { updateSearchParams } = useUpdateSearchParams();

<Link
  href={`/?${updateSearchParams("category", "test")}`}
  scroll={false}
>

```
