- 组件需前置下载shadcn
`npx shadcn@latest init`

# CustUpload
```tsx
"use client"
import axios from "axios";
import { useState } from "react";
import { Link as LinkIcon, Loader2, Trash2 } from 'lucide-react';
import { Input } from "../input";
import { Button } from "../../button";
import { Label } from "../../label";
import { Link } from "@/lib/navigation";

interface UploadProps extends React.HTMLAttributes<HTMLInputElement> {
    label: string;
    onUpload?: (fileList: FileListProps[]) => void;
}
type FileListProps = {
    originalFileName: string;
    previewUrl: string;
    token: string;
}

const Upload: React.FC<UploadProps> = ({ ...props }) => {

    const apiDomain = process.env.NEXT_PUBLIC_RESOURCE_URL;
    const [isLoading, setIsLoading] = useState<boolean>(false);
    const [fileList, setFileList] = useState<FileListProps[]>([]);


    async function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
        const file = e.target.files?.[0];
        if (!file) return
        setIsLoading(true);
        const formData = new FormData();
        formData.append('File', file);

        try {
            const url = ``;
            const res = await axios.post(url, formData, {
                headers: {
                    'Content-Type': 'multipart/form-data',
                    'Accept': "application/json"
                },
            });
            setIsLoading(false);
            if (res.data.code === 0) {
                const data = res.data.data;
                fileList.push(data);
                setFileList(fileList);
            }
        } catch (error) {
            console.log(error);
            setIsLoading(false);
        }
    }

    function removeIndex(index: number) {
        const arr = fileList.filter((_, i) => i !== index);
        setFileList(arr);
    }

    return (
        <div>
            {/* upload button */}
            <div>
                <Button variant="outline" type="button" disabled={isLoading}>
                    {isLoading && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
                    <Label className="cursor-pointer" htmlFor="files">
                        {props.label}
                    </Label>
                </Button>
                <Input
                    {...props}
                    id="files"
                    onChange={handleChange}
                    className="hidden"
                    type="file"
                />
            </div>
            {/* file list */}
            <ul>
                {fileList.map((file, index) => (
                    <li key={file.token} className="mt-2 flex items-center">
                        <LinkIcon className="w-[0.875rem] h-[0.875rem]" />
                        <Link
                            href={file.previewUrl}
                            target="_blank"
                            className="px-2 w-full text-[0.875rem]"
                        ><p>{file.originalFileName}</p></Link>
                        <Trash2 className="w-[0.875rem] h-[0.875rem] cursor-pointer" onClick={() => removeIndex(index)} />
                    </li>
                ))}
            </ul>
        </div>
    )
}

export default Upload;
```
