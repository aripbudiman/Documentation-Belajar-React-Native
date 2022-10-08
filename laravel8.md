# Documentastion Laravel by Arip Budiman

----------
### membuat update status dengan laravel

pada folder `view.blade.php`
```php
<div class="w-1/6">
    <div class="text-sm mb-1">Change Status</div>
        <a href="{{ route('transactions.changeStatus', ['id' => $item->id, 'status' => 'ON_DELIVERY']) }}"
                                    class="bg-blue-500 hover:bg-blue-700 text-white font-bold px-2 rounded block text-center w-full mb-1">
                                    On Delivery
        </a>
        <a href="{{ route('transactions.changeStatus', ['id' => $item->id, 'status' => 'DELIVERED']) }}"
                                    class="bg-green-500 hover:bg-green-700 text-white font-bold px-2 rounded block text-center w-full mb-1">
                                    Delivered
        </a>
        <a href="{{ route('transactions.changeStatus', ['id' => $item->id, 'status' => 'CANCELLED']) }}"
                                    class="bg-red-500 hover:bg-red-700 text-white font-bold px-2 rounded block text-center w-full mb-1">
                                    Cancelled
        </a>    </div>
```


pada folder `routes`
```php
Route::get('transactions/{id}/status/{status}',[TransactionController::class,'changeStatus'])->name('transactions.changeStatus');
```

pada folder `controller`
```php
public function changeStatus(Request $request,$id,$status){
        $transaction = Transaction::findOrFail($id);
        $transaction->status=$status;
        $transaction->save();
        return redirect()->route('transactions.show',$id);
    }
```


----------
