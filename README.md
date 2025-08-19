
Details are here:

https://n7sd.com/ada1-2/using-alire-to-edit-build-and-run-the-full-gtkada-testgtk-sample-suite/



Modify Source Files

=========================================
1. Modify src/create_label.adb
   
Reason: Non ASCII codes cause Error

=========================================

~/ada/testgtk/src$ gedit create_label.adb

Edit create_label.adb.

The line #153 has some Non-ASCII characters. 

Changed them to “XXXXXXXXXX”

Original: & “This one is underlined in ÆüËÜžì€ÎÆþÍÑquite a funky fashion”));

Change: & “This one is underlined in XXXXXXXXXXquite a funky fashion”));

The line #177 has some Non-ASCII characters. 

Changed them to “YYY”

Original: & “y=Î£x“

Change: & “y=YYY“


![image](https://github.com/user-attachments/assets/f3b6d263-56dd-456d-9fd1-d4d847d14ae5)



=========================================

2. Modify bin/css_accordion.css

Reason: Obsolete codes are used

=========================================

~/ada/testgtk/bin$ gedit css_accordion.css

Line #10 Add “-”

10 -icon-shadow: inherit;

Line #54 Delete engine command

54 /* engine: initial; */

Line #55 Add “-”

55 -gtk-key-bindings: initial;

Comment Out Line #57,58,59

57 /* -GtkWidget-focus-line-width: 0; */

58 / -GtkWidget-focus-padding: 0; */

59 / -GtkNotebook-initial-gap: 0; */

Change Line #65, 66

Before:

65

66 font: Cantarell 20px;

Change:

65 font-family: Cantarell;

66 font-size: 20px;


![image](https://github.com/user-attachments/assets/df011bbf-3842-4d55-9566-27729f534719)



=========================================

3. Modify src/create_range.adb
   
Reason: Bug fix, only “Range” is displayed in the windows.

        A lot of error messages on the Terminal.

=========================================

Line #

34   with GNAT.Strings;  –  Add this line

108 –  Comment Out 3 lines

109 --    Gtk_New (Scale_Button, Icon_Size_Button, 0.0, 100.0, 2.0,  --  Comment Out

110 --             Icons => (1 .. 0 => null                          --  Comment Out 

111 --    Pack_Start (Box3, Scale_Button, False, False, 0);          --  Comment Out



----------------------

 Add new Line #112 to #127:
 
----------------------

      declare
      
         Icons : GNAT.Strings.String_List := 
         
        (1 => new String'("audio-volume-muted"),
        
         2 => new String'("audio-volume-low"),
         
         3 => new String'("audio-volume-medium"),
         
         4 => new String'("audio-volume-high"));
         
      begin
      
         Gtk_New (Scale_Button, Gtk.Enums.Icon_Size_Button,
         
                  0.0, 100.0, 1.0, Icons);
                  
         --  Release to avoid memroy leak (each string individually)
         
         for I in Icons'Range loop
         
            GNAT.Strings.Free (Icons (I));
            
         end loop;         
         
         Pack_Start (Box3, Scale_Button, False, False, 0);   
         
      end;  

![image](https://github.com/user-attachments/assets/46f51624-f8f5-4d67-8228-795abc614f41)



=========================================

4. Modify create_color_chooser.adb
   
Reason: Application cannot close with Cancel button

=========================================

1, Add 2 lines at #30 

with Gtk.Button;   use Gtk.Button;     --  Line #30

with Gtk.Dialog;   use Gtk.Dialog;     --  Line #31

2, Add 2 lines at #37 (original #36), before “Diaglog : aliased…”

   --  Define the Widget_Handler package for Button callbacks            --  Line #37
   
   package Widget_Handler is new Handlers.Callback (Gtk_Widget_Record);  -- Line #38   


3, Add 7 lines at Line #70 (original #68), after end Destroy_Dialog

   ------------------
   
   --  Close_Window --    THE COPY OF create_color_selection.adb NEW ADDED
   
   ------------------
   
   procedure Close_Window (Win : access Gtk_Widget_Record'Class) is
   
   begin
   
      Destroy (Win);
      
   end Close_Window;   
   
4, Add 7 lines before Line #93 (original #84), before Show (Diablog)

         --  Widget_Handler was missing    
         
         Widget_Handler.Object_Connect
         
           (Gtk_Button
           
              (Get_Widget_For_Response (Dialog, Gtk_Response_Cancel)),
              
            "clicked",
            
            Close_Window'Access,
            
            Slot_Object => Dialog);     

![image](https://github.com/user-attachments/assets/f31305a2-e479-46a1-812e-575aea7f6244)


