# zodとは
バリデーションライブラリで、スキーマを定義し、バリデーションの設定を行うことができる。<br />
定義したスキーマからTypeScriptの型を生成することもできる。<br />

## zodが選ばれる理由
以下考慮されていることからzodが選ばれることが多いらしい。
- react-hook-formに対応している
- TypeScript対応（schemaから型を自動生成できる）
- githubのスター数
- 技術的な挑戦（メンバーが未経験のライブラリを使いたい）


上記を考慮した結果、Yup・Zod・Superstructが残り、「ドキュメントの豊富さ」・「生成される型の厳格さ」・「技術的挑戦」の観点を考慮して Zod を採用されることが多いらしい。

| ライブラリ  | ドキュメントの豊富さ　| 型の厳格さ | 技術的挑戦　|
|:------------|:----------------------|:-----------|:------------|
| Yup         | ○                     | △          | ×           |
| Zod         | △                     | ○          | ○           |
| Superstruct | ×                     | △          | ○           |

## React-Hook-FormとZodの基本的な使用例
### コード例
```
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import * as z from 'zod'
const schema = z.object({
  name: z.string().min(1, { message: 'Required' }),
  age: z.number().min(10),
})
type Schema = z.infer<typeof schema>
const App = () => {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<Schema>({
    resolver: zodResolver(schema), // zodResolver + schema
  })
  return (
    <form onSubmit={handleSubmit((d) => console.log(d))}>
      <input {...register('name')} />
      {errors.name?.message && <p>{errors.name?.message}</p>}
      <input type="number" {...register('age', { valueAsNumber: true })} />
      {errors.age?.message && <p>{errors.age?.message}</p>}
      <input type="submit" />
    </form>
  )
}

```
