+++
date        = "2009-10-07T12:00:00-04:00"
title       = "Simple HTTP POST in Java"
description = ""
tags        = [ "code", "java" ]
topics      = [ "code" ]
slug        = "simple-http-post-in-java"
+++

Today I was helping a friend debug a web service they had implemented. Their side was working correctly but the developer who was trying to interface with it seemed to be running into many problems. Since they were integrating an application written in Java, I whipped up a simple test for them. All we really needed to do was to send a few variables using HTTP POST to this resource and make sure it returned exactly what we were expecting.

This uses standard libraries only, and doesn't require anything third party. It does nothing fancy at all, just simply posts data to a URL. Hopefully you find this useful at some point.

<!--more-->

```
/*
 * Java POST Example
 */

package postit;

import java.io.IOException;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.ProtocolException;
import java.net.URL;
import java.net.URLEncoder;
import java.io.DataOutputStream;
import java.io.DataInputStream;
import java.util.logging.Level;
import java.util.logging.Logger;

/**
 *
 * @author Greg
 */
public class Main {

    /**
     * Pretend you're a script...
     */
    public static void main(String[] args) {
        final String server = "somewhere.ontheinter.net";

        URL url = null;
        try {
            url = new URL("http://" + server + "/we-expect-post/data");
        } catch (MalformedURLException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }

        HttpURLConnection urlConn = null;
        try {
            // URL connection channel.
            urlConn = (HttpURLConnection) url.openConnection();
        } catch (IOException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }

        // Let the run-time system (RTS) know that we want input.
        urlConn.setDoInput (true);

        // Let the RTS know that we want to do output.
        urlConn.setDoOutput (true);

        // No caching, we want the real thing.
        urlConn.setUseCaches (false);

        try {
            urlConn.setRequestMethod("POST");
        } catch (ProtocolException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }

        try {
            urlConn.connect();
        } catch (IOException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }

        DataOutputStream output = null;
        DataInputStream input = null;

        try {
            output = new DataOutputStream(urlConn.getOutputStream());
        } catch (IOException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }

        // Specify the content type if needed.
        //urlConn.setRequestProperty("Content-Type",
        //  "application/x-www-form-urlencoded");

        // Construct the POST data.
        String content =
          "name=" + URLEncoder.encode("Greg") +
          "&email=" + URLEncoder.encode("greg at code dot geek dot sh");

        // Send the request data.
        try {
            output.writeBytes(content);
            output.flush();
            output.close();
        } catch (IOException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }

        // Get response data.
        String str = null;
        try {
            input = new DataInputStream (urlConn.getInputStream());
            while (null != ((str = input.readLine()))) {
                System.out.println(str);
            }
            input.close ();
        } catch (IOException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }
    }
}
```
