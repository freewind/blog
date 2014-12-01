---
layout: post
id: 2796
alias: how-to-know-the-content-changes-of-editor-in-idea-plugin
tags: idea plugin
date: 2014-09-27 20:08:40
title: 在IdeaPlugin中如何知道当前editor中的内容发生了变化？
---

每一个文件打开后，都会有一个对应的`com.intellij.openapi.fileEditor.FileEditor`实例。它有一个方法`getEditor`可以拿到一个`com.intellij.openapi.editor.Editor`实例。`Editor`实例中，有一个`getDocument`方法，可以拿到一个`Document`实例。在这个实例中，我们可以添加一些`DocumentListener`，实时监视内容的变化。

示例代码如下（假设我们有一个editor）：

```scala
editor.getDocument.addDocumentListener(new DocumentListener {

  override def beforeDocumentChange(event: DocumentEvent) {
    println("## beforeDocumentChanged: " + event)
  }

  override def documentChanged(event: DocumentEvent) {
    println("## documentChanged: " + event)
  }

}
```

可以看到，每当文档中有内容发生变化时，这两个回调方法都会被调用，一个在前一个在后。其参数`DocumentEvent`会告诉我们，文档发生了什么变化。

比如，我在文档中输入了一个字母`a`后，会打印出两条信息：

```
## beforeDocumentChanged: DocumentEventImpl[myOffset=73, myOldLength=0, myNewLength=1, myOldString='', myNewString='a'].
## documentChanged: DocumentEventImpl[myOffset=73, myOldLength=0, myNewLength=1, myOldString='', myNewString='a'].
```

它会告诉我们哪个位置的内容发生了变化，以及改变的内容是什么样子的。

## editor从哪里来

### 直接拿到当前被选中的editor

我知道有两种方法，一个是全局的用来拿到“当前被选中的editor”：

```
FileEditorManager.getInstance(project).getSelectedTextEditor
```

这个方法可以直接拿到当前project中被选中的那个TextEditor，其类型为`Editor`.

但是对于我想做的事情来说，这个方法用不上。我想给每一个“当前”editor添加一个`DocumentListener`。由于每个文件都可以被多次打开、关闭，我必须想办法准确拿到当前操作的editor。

### 通过监听editor切换事件

我们可以通过添加一个`FileEditorManager`去监听当前项目各editor之间切换、开关的事件：

```scala
val connection: MessageBusConnection = project.getMessageBus.connect(project)
connection.subscribe(FileEditorManagerListener.FILE_EDITOR_MANAGER, new FileEditorManagerAdapter {
  override def fileOpened(@NotNull source: FileEditorManager, @NotNull file: VirtualFile) {}

  override def fileClosed(source: FileEditorManager, file: VirtualFile) {}

  override def selectionChanged(event: FileEditorManagerEvent) {
    val oldEditor = Option(event.getOldEditor)
    val newEditor = Option(event.getNewEditor)

    oldEditor match {
      case Some(x: TextEditor) => removeDocumentListener(x.getEditor)
      case _ =>
    }
    newEditor match {
      case Some(x: TextEditor) => addDocumentListener(x.getEditor)
      case _ =>
    }
  }
})

```

当我们在`selectionChanged`回调中，通过事件对象拿到发生切换的两个`FileEditor`后，就可以拿到每一个相应的`Editor`实例，为其添加或者移除listener.
