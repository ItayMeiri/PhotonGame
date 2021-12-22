This project demonstrates the Photon capabilities


In order to create the shield, I added a field that regenerates every X seconds, which then activates a shield with a certain value:

```csharp
        //For shielding the player
        private float shield;
        
```

Then, I used the `IPunObservable` implementation to stream the object:

```csharp

        public void OnPhotonSerializeView(PhotonStream stream, PhotonMessageInfo info)
        {
            if (stream.IsWriting)
            {
                // We own this player: send the others our data
                stream.SendNext(this.IsFiring);
                stream.SendNext(this.Health);
                stream.SendNext(this.shield);
            }
            else
            {
                // Network player, receive data
                this.IsFiring = (bool) stream.ReceiveNext();
                this.Health = (float) stream.ReceiveNext();
                this.shield = (float)stream.ReceiveNext();
            }
        }
        
```


This updates the shield through the Photon engine. 

We changed the trigger / damage so that it'll damage the shield instead of the player:

```csharp

        public void OnTriggerEnter(Collider other)
        {
            if (other.tag == triggeringTag && photonView.IsMine)
            {
                if (this.shield > 0)
                {

                    this.shield -= 0.1f;

                }
                else
                {
                    this.Health -= 0.1f;
                }
            }
        }
```

So the shield can take 10 total "hits" (or continuously firing for a very short time)
