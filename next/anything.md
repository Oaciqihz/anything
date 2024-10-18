
# Route Handlers 处理文件展示请求
`/app/download/[id]/route.ts`
```tsx
import { cookies } from "next/headers";
import { notFound } from "next/navigation";
import { NextResponse } from "next/server";

export async function GET(request: Request, {params}: {params: {id: number}}) {
    const key = cookies().get("token");
    const filename = `payment-${params.id}.pdf`;
    const apiDomain = process.env.NEXT_PUBLIC_API_URL;

    try {
      const response = await fetch(`${apiDomain}/api/download-pdf/${params.id}`, {
        method: "GET",
        headers: {
          'token': key?.value||"",
        },
      })
      if (!response.ok) {
        return notFound();
      // return NextResponse.json({ error: 'Failed to fetch file' }, { status: response.status });
      }

      return new NextResponse(response.body, {
        headers: {
          'Content-Type': 'application/pdf',
          'Content-Disposition': `inline; filename=${filename}`, //  'inline' 关键在于这里，它告诉浏览器直接在浏览器中显示PDF
        },
      });

      // 下載文件 ===>
      // const blob = await response.blob();
      // const stream = new ReadableStream({
      //   start(controller) {
      //     const reader = blob.stream().getReader();
      //     return pump();
      //     async function pump(): Promise<void> { // Added return type annotation
      //       const { done, value } = await reader.read();
      //       if (done) {
      //         controller.close();
      //         return;
      //       }
      //       controller.enqueue(value);
      //       return pump(); // Recursive call is now correctly typed
      //     }
      //   },
      // });
      

      // return new NextResponse(stream, {
      //   headers: {
      //     'Content-Type': response.headers.get('content-type') || 'application/octet-stream',
      //     'Content-Disposition': `attachment; filename="${response.headers.get('content-disposition') || 'file.pdf'}";`, // 设置文件名
      //     'Content-Length': response.headers.get('content-length') || '', // 设置文件大小
      //   },
      // });
      
    } catch (error: any) {
      // return NextResponse.json({ error: error?.message||"" }, { status: 500 });
        return notFound();
    }
```
