---
title: How Webkit render a button
author: Fan Fan
layout: post
permalink: /2011/06/24/how-webkit-render-a-button/
dsq_thread_id:
  - 604410541
categories:
  - UI
---
看了这位[老兄的blog][1]后，我对webkit也有了兴趣。webkit可以根据布局结构来动态的产生HTML控件，这个在很多可视化开发工具里面可以看到，比如Netbeans，XCode都可以用拖放的方式开发UI。我原来很好奇，这些IDE是如何显示这些控件的呢？因为这些控件和IDE本身的控件有一致外观（OS的主题外观），如果使用普通的API来搞，比如&#8221;new JButton()&#8221;来构造UI，感觉有点头晕。

找了下，webkit的代码Source\WebCore\platform\gtk\RenderThemeGtk3.cpp是用来创建Gnome 3外观风格的控件，比如方法paintToggle就是对应单选框。我把里面的代码抽取出来，做了个小例子。费了老大劲，因为压根不知道如何得到context和cairo_t这些上下文信息（或者称为状态机，Functional Language反感的就是这个）。GTK的getting started在[这里][2]。

<pre class="brush:c">/**
  this code show how to render a button with gtk 3
  Some code copy from webkit/Source/WebCore/platform/gtk
*/

#include &lt;gtk/gtk.h&gt;
static gboolean
draw_cb (GtkWidget *widget, cairo_t *cr){
  GtkStyleContext *context;
  context = gtk_widget_get_style_context (widget);
  gtk_style_context_save(context);
  gtk_style_context_add_class(context,GTK_STYLE_CLASS_CHECK );
  gtk_style_context_set_state(context, GTK_STATE_FLAG_ACTIVE);

  int i;
  for(i = 0; i &lt; 20; i++){
    <strong>gtk_render_check</strong>(context, cr, 10*(i+1), 10*(i+1), 10, 10);
  }
  gtk_style_context_restore(context);
}

int
main (int   argc, char *argv[]){
  GtkWidget *window;
  GtkWidget *ebox;

  gtk_init (&argc, &argv);

  window = gtk_window_new (GTK_WINDOW_TOPLEVEL);
  ebox = gtk_event_box_new ();
  gtk_event_box_set_visible_window (GTK_EVENT_BOX (ebox), TRUE);
  gtk_container_add (GTK_CONTAINER (window), ebox);
  g_signal_connect_after (ebox, "draw", G_CALLBACK (draw_cb), NULL);
  g_signal_connect (window, "destroy", G_CALLBACK (gtk_main_quit), NULL);

  gtk_widget_show_all (window);

  gtk_main ();

  return 0;
}</pre>

[<img class="alignnone size-full wp-image-1021" title="native-check" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/06/native-check.png" alt="" width="532" height="495" />][3]

上面的截图中可以看到checkbox有native的looking（本人非Nike控），不同的是这些控件只是画上去的（没有JButton这种类的层次结构），没有event binding，也就是MVC只有MV。C的一块可能就由浏览器接过来来统一管理，那得遵循HTML Dom Event Model的规范。

这里不考虑CSS的外观。Gnome 3也是用CSS来控制外观，不知道是不是借鉴webkit。Windows的theme我还没有找到在哪里。

从webkit的代码看其已经支持Gtk+-3.0的back-end，不过Chrome还没有推出相应版本，据说是因为他跟Gtk+-2.0绑的太紧，跟Ubuntu走的太近（Ubuntu没有支持gnome 3）。唉，才几年又轻又快的东东就成了个史前巨无霸。

 [1]: http://www.grati.org/?p=559
 [2]: http://developer.gnome.org/gtk3/3.1/gtk-getting-started.html
 [3]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/06/native-check.png