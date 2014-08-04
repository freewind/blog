---
layout: post
title: Robolectric例子跑通
tags:
  - Android
date: 2013-01-01 11:24:19
---

Robolectric: [http://pivotal.github.com/robolectric/](http://pivotal.github.com/robolectric/)

Android测试太慢了，每次发布再加上从头点起，最快也要1分钟才能开始手动测试，非常浪费时间。

今天把这个跑通了，很简单，在junit中跑测试非常快，几秒钟就测完。

使用方法不像官网上写的那么误导人，不需要maven，直接把robolectric-1.2-20120910.162821-126-jar-with-dependencies.jar和junit-xxx.jar拷到项目中，并且把它们两个上移到依赖的最上方即可。

然后像下面写测试即可：

<div class="mycode">

import android.app.Activity;     
import android.widget.Button;      
import android.widget.TextView;      
import com.example.MyActivity;      
import com.example.R;      
import com.xtremelabs.robolectric.RobolectricTestRunner;      
import org.junit.Before;      
import org.junit.Test;      
import org.junit.runner.RunWith;

import static org.hamcrest.core.IsEqual.equalTo;     
import static org.junit.Assert.assertThat;

@RunWith(RobolectricTestRunner.class)     
public class MyActivityTest {      
    private MyActivity activity;      
    private Button pressMeButton;      
    private TextView results;

    @Before     
    public void setUp() throws Exception {      
        activity = new MyActivity();      
        activity.onCreate(null);      
        pressMeButton = (Button) activity.findViewById(R.id.press_me_button);      
        results = (TextView) activity.findViewById(R.id.results_text_view);      
    }

    @Test     
    public void shouldUpdateResultsWhenButtonIsClicked() throws Exception {      
        pressMeButton.performClick();      
        String resultsText = results.getText().toString();      
        assertThat(resultsText, equalTo("Testing Android Rocks!"));      
    }      
}

</p></div>

 

目前只跑了它最基本的例子，没问题，不知道测复杂的功能时会怎么样。我现在在用ormlite写一些数据库测试的代码，这些东西要手动测的话麻烦死，希望能用这个测。等这两天写了测试后再过反馈过来。

原理：Android上的vm跟电脑上的jvm不是一回事，按说是不能通用的。Robolectric是通过一些shadow类，模拟android的功能，拦截我们代码对于底层的调用，然后使用它提供的方法响应。这要才能做到在jvm上测android功能。

据说它实现了大部分android的功能，也就是说，还有一部分没有实现，目前还不知道是哪些。希望我用到的都实现了。