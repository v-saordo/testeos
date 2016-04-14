
A continuación, deberá cambiar el momento en que se registra para recibir notificaciones para asegurarse de que el usuario se autentique antes de que se intente el registro.


1. En el Explorador de paquetes de Eclipse, abra el archivo ToDoActivity.java y busque el método `onCreate`. Mueva el siguiente código del método `onCreate` al comienzo del método `createTable`.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

     Se llama al método `createTable` cuando se complete el método `authenticate`. El método `createTable` completo debe tener un aspecto similar al siguiente.

        private void createTable() {
        
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
            
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
            
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
            
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }	

<!---HONumber=August15_HO6-->