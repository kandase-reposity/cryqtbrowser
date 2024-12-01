#!/usr/bin/env python

import sys
import json
from PySide6.QtCore import *
from PySide6.QtWidgets import QApplication, QMainWindow, QTabWidget, QVBoxLayout, QWidget, QToolBar, QLineEdit, QDialog, QDialogButtonBox, QStatusBar, QPushButton, QListWidget, QMessageBox
from PySide6.QtGui import QAction
from PySide6.QtWebEngineWidgets import QWebEngineView

class Bookmark:
    def __init__(self, title, url):
        self.title = title
        self.url = url

    def to_dict(self):
        return {"title": self.title, "url": self.url}

    @staticmethod
    def from_dict(data):
        return Bookmark(data['title'], data['url'])

class BookmarkManager:
    def __init__(self, filename='bookmarks.json'):
        self.bookmarks = []
        self.filename = filename
        self.load_bookmarks()

    def add_bookmark(self, bookmark):
        self.bookmarks.append(bookmark)
        self.save_bookmarks()

    def remove_bookmark(self, bookmark):
        self.bookmarks.remove(bookmark)
        self.save_bookmarks()

    def get_bookmarks(self):
        return self.bookmarks

    def save_bookmarks(self):
        with open(self.filename, 'w') as f:
            json.dump([bookmark.to_dict() for bookmark in self.bookmarks], f)

    def load_bookmarks(self):
        try:
            with open(self.filename, 'r') as f:
                bookmarks_data = json.load(f)
                self.bookmarks = [Bookmark.from_dict(data) for data in bookmarks_data]
        except (FileNotFoundError, json.JSONDecodeError):
            self.bookmarks = []

class BrowserTab(QWidget):
    def __init__(self, proxy=None):
        super().__init__()
        self.layout = QVBoxLayout()
        self.browser = QWebEngineView()
        self.browser.setUrl(QUrl("http://www.google.com"))
        self.layout.addWidget(self.browser)
        self.setLayout(self.layout)

class Browser(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("cryqtbrowser git")
        self.setGeometry(100, 100, 1200, 800)

        self.tabs = QTabWidget()
        self.setCentralWidget(self.tabs)
        self.add_new_tab()

        self.bookmark_manager = BookmarkManager()

        
        self.menu_bar = self.menuBar()
        self.bookmark_menu = self.menu_bar.addMenu("bookmark")

        
        self.add_bookmark_action = QAction("add bookmark", self)
        self.add_bookmark_action.triggered.connect(self.add_bookmark)
        self.bookmark_menu.addAction(self.add_bookmark_action)

        self.view_bookmarks_action = QAction("savd bookmarks", self)
        self.view_bookmarks_action.triggered.connect(self.view_bookmarks)
        self.bookmark_menu.addAction(self.view_bookmarks_action)

        
        self.tabs.setTabsClosable(True)
        self.tabs.tabCloseRequested.connect(self.close_current_tab)

        
        self.toolbar = QToolBar()
        self.addToolBar(self.toolbar)

        
        self.new_tab_button = QPushButton("new tab")
        self.new_tab_button.clicked.connect(self.add_new_tab)
        self.toolbar.addWidget(self.new_tab_button)

        
        self.back_button = QPushButton("back")
        self.back_button.clicked.connect(self.back)
        self.toolbar.addWidget(self.back_button)

        self.forward_button = QPushButton("forward")
        self.forward_button.clicked.connect(self.forward)
        self.toolbar.addWidget(self.forward_button)

        self.refresh_button = QPushButton("refresh")
        self.refresh_button.clicked.connect(self.refresh)
        self.toolbar.addWidget(self.refresh_button)

        
        self.url_bar = QLineEdit()
        self.url_bar.returnPressed.connect(self.navigate_to_url)
        self.toolbar.addWidget(self.url_bar)

        
        self.status_bar = QStatusBar()
        self.setStatusBar(self.status_bar)

        
        self.tabs.currentChanged.connect(self.update_url_bar)

        
        self.proxy = None
        self.add_new_tab()

    def add_new_tab(self):
        new_tab = BrowserTab()
        self.tabs.addTab(new_tab, "New Tab")
        self.tabs.setCurrentWidget(new_tab)

    def close_current_tab(self, index):
        if self.tabs.count() > 1:
            self.tabs.removeTab(index)

    def back(self):
        current_tab = self.tabs.currentWidget()
        if current_tab.browser.history().canGoBack():
            current_tab.browser.back()

    def forward(self):
        current_tab = self.tabs.currentWidget()
        if current_tab.browser.history().canGoForward():
            current_tab.browser.forward()

    def refresh(self):
        current_tab = self.tabs.currentWidget()
        current_tab.browser.reload()

    def navigate_to_url(self):
        current_tab = self.tabs.currentWidget()
        url = self.url_bar.text()
        if not url.startswith("http://") and not url.startswith("https://"):
            url = "http://" + url
        current_tab.browser.setUrl(QUrl(url))

    def update_url_bar(self):
        current_tab = self.tabs.currentWidget()
        self.url_bar.setText(current_tab.browser.url().toString())
        self.status_bar.showMessage(f"succesful connect: {current_tab.browser.url().toString()}")

    def add_bookmark(self):
        current_tab = self.tabs.currentWidget()
        title = current_tab.browser.title()
        url = current_tab.browser.url().toString()

        if title and url:
            bookmark = Bookmark(title, url)
            self.bookmark_manager.add_bookmark(bookmark)
            QMessageBox.information(self, "succesful", f"bookmark '{title}' added")
        else:
            QMessageBox.warning(self, "error", "error")

    def view_bookmarks(self):
        bookmarks = self.bookmark_manager.get_bookmarks()
        dialog = QDialog(self)
        dialog.setWindowTitle("bookmarks")

        layout = QVBoxLayout(dialog)
        list_widget = QListWidget(dialog)

        for bookmark in bookmarks:
            list_widget.addItem(f"{bookmark.title} - {bookmark.url}")

        layout.addWidget(list_widget)
        dialog.setLayout(layout)

        dialog.exec()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    browser = Browser()
    browser.show()
    sys.exit(app.exec())
