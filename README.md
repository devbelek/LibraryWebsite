start a project command:
django-admin startproject bookapp

go to bookapp directory:
cd bookapp

create a new app:
django-admin startapp bookstore

link paths in settings.py (e.g. template and static)

In case you get an error after command python manage.py makemigrations bookstore, in your Installed_Apps settings comment out django.contrib.admin. Then run
python manage.py migrate.
When done uncomment 'django.contrib.admin'

If you get The error msg is "No install app with label admin" - comment path('admin/', admin.site.urls) in urls.py

in views.py 'loginView' function asks the user what type of users he is (e.g. admin, publisher or librarian). Depending on the type of the user the user is redirected to his profile. In order to avoid the confusion functions and URLs of each user are separated.

import " from django.contrib.auth.hashers import make_password " to create the librarian user in the shell then type the following commands:

python3 manage.py shell
In [1]: from bookstore.models import User

In [2]: from django.contrib.auth.hashers import make_password

In [3]: a = User()

In [4]: a.username = "test"

In [5]: a.password = make_password("test")

In [6]: a.is_librarian = True

In [7]: a.save()
the same we can do for publisher user

In order to let the publisher user to log in successfully I created "class PBookListView" in "views.py" and gave it the url "path('publisher/', views.PBookListView.as_views(), name='publisher')". It redirects to 'book_list.html' templates.

publisher adds a book:
-"def uabook(request)" in views.py redirects to "add_book.html" where the user can fill in all data about the book.
-"def uabook(request)" in views.py adds the book to the database
-in order to list all books on the page I created a for loop in "book_list.html" and pagination

Group Chat:
create a class Chat in models.py
create a form in forms.py
"class UCreateChat" allows the user to create a chat, it redirects to "chat_form.html" where the user submits the context via method="POST".
If we are using success_url we have to use reverse_lazy() which indicates to wich url the user will be redirected.
"class UListChat" in "view.py" returns the list of chats.

Delete Request:
user who uploaded a book can request the admin to delete it. The user can make a request only about deleting his own uploaded books.
-"def request_form(request)" is responsible for a form where the user can type in the id of the book which he would like to delete. This form has a POST method in the "delete_request.html" file.
-in order to actually send this request successfully I created a class "DeleteRequest" in the models.py and the view function "def delete_request(request)" in the views.py. When the delete request is sent via POST method the user is redirected again to the request form. However the book must still be listed on the publisher dashboard. The book is still not deleted but the Admin will recieve a notification with the text: "username + " wants the book with the id " + book_id + " to be deleted"". This text is stored in the variable "user_request" in the "delete_request" function in the views.py.

Send Feedback:
"def feedback_form(request)" in the views.py redirects you to the form in "send_feedback.html" where via POST method you send the feedback. The function "def send_feedback(request)" in the views.py also has a variable ("feedback") which stores the actual text of the feedback which will be sent to the admin. After sending the feedback the user is redirected again to the feedback form and recieves a message whether the feedback was sent or not.

About Us:
"def about(request)" in the views.py redirects to the "about.html" file where the user sees short description about this app.

Search:
the function "def usearch(request)" in the views.py returns the book the id of which the user types in the search function on the top right corner in the navigation bar. This function chains querysets together with the chain(_) function of the itertools package.

Librarian account has its own functions in the view.py as well as html files. On the librarian dashboard the user sees 2 boxes. One with the amount of books and another with the amount of Users. The amount of each group (e.g. Books, Users) is implemented in the function "def librarian(request)" in the views.py with the help of Model.objects.all().count().

Librarian adds a book:
in the views.py "def labook_form(request)" redirects to the form. Via POST method the user submits data about the book. enctype must be "multipart/form-data" which allows the user to choose a file. Similar to publisher "uabook" function here "def labook(request)" function in the views.py adds the book to the database. It requires the user to provide all the information about the book (e.g. author, publisher, title, description), to upload the cover and the pdf file.

Recent Added Books:
class "class LBookListView(LoginRequiredMixin, ListView)" redirects to "book_list.html" file which shows the list of books. The list starts from the recent one on top (Book.objects.order_by('-id')) and has Pagination (paginate_by = 2).

Manage Books:
"class LManageBook(LoginRequiredMixin, ListView)" redirects a list of all books from the database. Librarian can View, Edit and Delete each book listed on the page. These 3 buttons are present in the "manage_books.html" file.

Delete Request:
"class LDeleteRequest(LoginRequiredMixin,ListView)" in the views.py redirects to 'librarian/delete_request.html'. 'feedbacks' is a context_object_name which lists all feedbacks from other users ({{ feedback.delete_request }}). Here "delete_request" returns the value that was sent from the variable "user_request" from the publisher functions in the views.py namely "delete_request" function.

Search book:
this function is the same as for the publisher account. The user just types in the id of a book and sees the searched book listed on the page. This function is implemented with the help of itertools.chain().

Delete A Book:
both "class LDeleteBook" and "class LDeleteView" are responsible for deleting a book. The former deletes a book when the librarian searches a book and the latter deletes a book through the Manage Book section. Thats why these two function redirect to different urls after deleting a book.

View Book:
clicking on the button View the user can see the information about the title, author, description ..(see "book_detail.html").

Edit Book:
 I used a django Form in order to modify the data. in the "edit_book.html" I rendered each field of the form by {{ field.label_tag }} {{ field }}.

Admin Group Chat:
like publisher account, the librarian account also can create a chat as well as see all posts. "class LCreateChat" in the views.py creates a post. "class LListChat" shows all posts. the last posted chats should be on top ("order_by('-id')").

Add book, Recent Added Books:
the same as for publisher and librarian account.

Delete request, Manage Book together with View, Edit and Delete Functions is the same as for librarian account.

User Feedback:
here admin can see the user's Feedback. "class AFeedback" in views.py redirects to "feedback.html". From there the value of feedback is rendered from the database, namely the value of feedback that was sent via "def send_feedback(request)" function by publisher account.

Manage User:
this function is available only for Admin account. in the "def create_user_form" function in the views.py, there is a variable choice "choice = ['1', '0', 'Publisher', 'Admin', 'Librarian']" where publisher account is choice 2, Admin account is choice 3 and Librarian account is choice 4 as shown below (see "add_user.html")
<option value="{{ choice.2 }}"> Publisher</option>
<option value="{{ choice.3 }}"> Admin</option>
<option value="{{ choice.4 }}"> Librarian</option>
"class ListUserView" in views.py redirects to "list_users.html" and shows all users in the app, information (such as, Username, user role and email), as well as functions View, Edit and Delete. I used "Django’s generic views".
