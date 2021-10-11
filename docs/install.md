# Installation
xmrSale needs a Monero node to check the Monero blockchain for payments. If you don't have a node, you can follow the Docker installation below which will install one for you. Or you can [install one](https://sethforprivacy.com/guides/run-a-monero-node-advanced/) yourself. While you can run xmrSale on the same machine as your Monero node, a separate VPS for xmrSale is recommended.
## Install (with node)
Clone and install dependencies
```
git clone https://github.com/xmrsale/xmrSale
cd xmrSale/
pip3 install -r requirements.txt
```
### Connect to your Monero Node
Edit the `config.py` configuration and point to your Monero node:
```python
host = "127.0.0.1"
monerod_rpcport = "18081"
monerowallet_rpcport = "18090"
monerod_username = "MONERODRPC_USER"
monerod_password = "MONERODRPC_PASS"
wallet_username = "WALLETRPC_USER"
wallet_password = "WALLETRPC_PASS"
```
You can find these monerod rpc details in `monero.conf` or in daemon arguments within your systemd `.service`.

To be able to connect to your node with full ability to create addresses, we need to have a `monero wallet RPC` service running alongside our monerod RPC. Note most node guides do not install this by default, including sethforprivacy's.

To run `monero-wallet-rpc` with arguments, it is probably easiest to run this as a service like you may have done for your monerod daemon. We  will just create a similar `.service`, here is an example [monerowallet.service](https://github.com/xmrsale/xmrSale/blob/master/docs/monerowallet.service). You can set the wallet RPC login as arguments in this file.

Connecting to a remote node is easy and can be done over SSH tunneling or tor hidden services (tor not yet working), you can do this manually with
```
ssh root@IP -q -N -L 18081:localhost:18081 &
ssh root@IP -q -N -L 18090:localhost:18090 &
```
or whatever ports you're using. Make sure your ssh connection is working (without prompts!). Further config examples can be found in [docs/](docs/).

### Run xmrSale
Run xmrSale with
```
gunicorn -w 1 -b 0.0.0.0:8000 xmrsale:app
```
Gunicorn is a lightweight python HTTP server, alternatively you can run with just `python xmrsale.py` though this is not recommended for production.

That's it! You should now be able to view your xmrSale server at `http://YOUR_SERVER_IP:8000/`. If running locally, this will be `127.0.0.1:8000`.

If running on a Raspberry Pi, you will want to [forward port 8000 in your router settings](https://user-images.githubusercontent.com/24557779/105681219-f0f5fd80-5f44-11eb-942d-b574367a161f.png) so that xmrSale is also visible at your external IP address. You might have to allow gunicorn through your firewall with `sudo ufw allow 8000`.

You will want to run gunicorn with nohup so it continues serving in the background:
```
nohup gunicorn -w 1 0.0.0.0:8000 xmrsale:app > log.txt 2>&1 &
tail -f log.txt
```

## Install (with Docker)
A beta docker image is only semi working and has **not yet been security vetted** and is not ready for production use, it is packaged with a Monero node for an easy installation:
For testing:
```
git clone https://github.com/xmrsale/xmrSale
cd xmrSale/
```
Edit `docker-compose.yml` with links to your Monero wallet file and blockchain directory (if applicable). Then
```
docker-compose up --build
```
it will have to sync with the Monero blockchain which may take some time.

## Embed a Donation Button
Now we have xmrSale running, let's embed the donation button into a website HTML:
```html
<iframe src="http://YOUR_SERVER_IP:8000/" style="margin: 0 auto;display:block;width:420px;height:460px;border:none;overflow:hidden;" scrolling="no"></iframe>
```
Change `YOUR_SERVER_IP` to the IP address of the machine you're running xmrSale on, node or otherwise. Additionally, you could redirect a domain to that IP and use that instead.
