# Run

### From a local Python class

```python
from jina import Executor, requests, DocumentArray, Document


class MyExec(Executor):
    @requests
    def foo(self, docs, **kwargs):
        for d in docs:
            d.text = 'hello world'


executor = MyExec()
docs = DocumentArray([Document(text='hello')])

executor.foo(da)
print(f'Text: {docs[0].text}')
```

```text
Text: hello world
```

### From Executor Hub

[Executor Hub](https://cloud.jina.ai/executors) is Jina AI's marketplace for Executors, letting you pull Executors to your local machine without getting your hands dirty implementing functionality from scratch. Check the {ref}`docs <jina-hub>` for more information.

```python
from docarray import Document, DocumentArray
from jina import Executor

executor = Executor.from_hub(
    uri='jinaai://jina-ai/CLIPTextEncoder', install_requirements=True
)

docs = DocumentArray(Document(text='hello'))
executor.encode(docs, {})

print(docs.embeddings.shape)
```
```text
(1, 512)
```

## Run as a process

You can run an Executor from CLI. In this case, the Executor occupies one process. The lifetime of the Executor is the lifetime of the process.

### From a local Executor

With `exec.py` containing the definition of `MyExec`, now creating a new file called `my-exec.yml`:

```yaml
jtype: MyExec
py_modules:
  - exec.py
```

This simply points Jina to our file and Executor class. Now we can run the command:

```bash
jina executor --uses my-exec.yml --port 12345
```

### From Executor Hub

In this example, we use [`CLIPTextEncoder`](https://cloud.jina.ai/executor/livtkbkg) to create embeddings for our Documents.

````{tab} With Docker

```bash
jina executor --uses jinaai+docker://jina-ai/CLIPTextEncoder
```

````

````{tab} Without Docker

```bash
jina executor --uses jinaai://jina-ai/CLIPTextEncoder
```

````

This might take a few seconds, but in the end you should be greeted with the
following message:

```bash
WorkerRuntime@ 1[L]: Executor CLIPTextEncoder started
```

Just like that, our Executor is up and running.
