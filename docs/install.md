# Installation
If you want to run on your own node or docker, please checkout this [original branch](https://github.com/xmrsale/xmrSale/tree/original).

First, clone and install dependencies
```
git clone https://github.com/xmrsale/xmrSale
cd xmrSale/
pip3 install -r requirements.txt
```
## xmrSale Setup
Run
```
python3 xmrsale.py --setup
```
This will download monero-cli tools from `downloads.getmonero.org` which are used to manage payments.
Enter a new password for your new `xmrsale_wallet` (and `.keys`).
You **must backup your passphrase and seedphrase**.

Click `N` when asked whether you want to mine in the background, then type `exit`.

Now xmrsale will run the `monero-wallet-rpc` and sync with a public node (`node.moneroworld.com`). Once you see `Refresh done, blocks received...` you are ready to run xmrsale! If you want, you can adjust the `config.py` to your liking. You can always sync with `python3 xmrsale.py --sync`.

### Start xmrSale
Run xmrSale with
```
gunicorn xmrsale:app --timeout 120
```
That's it! If running locally, this will be `127.0.0.1:8000`. If you allow traffic on that port you should now also be able to view your xmrSale at `http://YOUR_IP:8000/`. If you're running on a VPS you may need to `ufw allow 8000` or make an [nginx config](https://github.com/xmrsale/xmrSale/blob/master/docs/HTTPS.md).

You will want to run gunicorn with nohup so it continues serving in the background:
```
nohup gunicorn xmrsale:app > log.txt 2>&1 &
tail -f log.txt
```

### Embed a Donation Button
Now we have xmrSale running, let's embed the donation button into a website HTML:
```html
<iframe src="http://YOUR_SERVER_IP:8000/" style="margin: 0 auto;display:block;width:420px;height:460px;border:none;overflow:hidden;" scrolling="no"></iframe>
```
Change `YOUR_SERVER_IP` to the IP address of the machine you're running xmrSale on, node or otherwise. Additionally, you could redirect a domain to that IP and use that instead.
