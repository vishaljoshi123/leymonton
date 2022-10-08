# leymonton
import discord
from discord.ext import commands
from youtube_dl import YoutubeDL

BOT = commands.Bot(command_prefix='404')

<?php
$MERCHANT_KEY = "ZHsuRZmv";
$SALT = "BL3uaRBzfK";
// Merchant Key and Salt as provided by Payu.

///$PAYU_BASE_URL = "https://sandboxsecure.payu.in";		// For Sandbox Mode
$PAYU_BASE_URL = "https://secure.payu.in";			// For Production Mode
$posted = array();
if(!empty($_POST['txnid'])) {
    //print_r($_POST);
  foreach($_POST as $key => $value) {    
    $posted[$key] = $value; 
  }
}

$formError = 0;

if(empty($posted['txnid'])) {
  // Generate random transaction id
  $txnid = substr(hash('sha256', mt_rand() . microtime()), 0, 20);
} else {
  $txnid = $posted['txnid'];
}
$hash = '';
// Hash Sequence
$hashSequence = "key|txnid|amount|productinfo|firstname|email|udf1|udf2|udf3|udf4|udf5|udf6|udf7|udf8|udf9|udf10";
if(empty($posted['hash']) && sizeof($posted) > 0) {
  if(
          empty($posted['key'])
          || empty($posted['txnid'])
          || empty($posted['amount'])
          || empty($posted['firstname'])
          || empty($posted['email'])
          || empty($posted['phone'])
          || empty($posted['productinfo'])
          || empty($posted['surl'])
          || empty($posted['furl'])
		  || empty($posted['service_provider'])
  ) {
    $formError = 1;
  } else {
    //$posted['productinfo'] = json_encode(json_decode('[{"name":"tutionfee","description":"","value":"500","isRequired":"false"},{"name":"developmentfee","description":"monthly tution fee","value":"1500","isRequired":"false"}]'));
	$hashVarsSeq = explode('|', $hashSequence);
    $hash_string = '';	
	foreach($hashVarsSeq as $hash_var) {
      $hash_string .= isset($posted[$hash_var]) ? $posted[$hash_var] : '';
      $hash_string .= '|';
    }

    $hash_string .= $SALT;

    $prod=$obj_database->selectAllData(TB_PREFIX."item","id='".$_POST['product']."'","desc","id");

    $r=rand(100,999);
	$data['bill_no']="GDI-".date('Y-m-d')."/".$r;
	$data['date']=date('Y-m-d');
	$data['user_id']=$_SESSION['user_detail']['id'];
	$data['amount']=$_SESSION['cart']['total'];
	$data['goodmart_point']=get_account_balance($_SESSION['user_detail']['user_id']);
	$wp=get_win_balance($_SESSION['user_detail']['user_id']); $lwp=($data['amount']*10/100>$wp)?$wp:$data['amount']*10/100;
	$data['win_point']=$lwp;
	$data['payable']=$data['amount']-$data['goodmart_point']-$data['win_point'];
	$data['status']=0;
	$data['shipping']=$_SESSION['shipping'];
	$data['shipping_address'] = 'Name : '.  $posted['firstname'] . '; ' . 'Phone : ' . $posted['phone']. '; ' .'Address : ' . $posted['address1'] . ', ' . $posted['city'] . ', ' . $posted['state'] . ', ' . $posted['zipcode'];
	$data['payment_mode']=1;
	
		if($obj_database->insertData($data,TB_PREFIX."invoice"))
				{
				    $bill_id=$obj_database->get_last_id();
					   foreach($_SESSION['cart']['item'] as $i) 
					   {
					       $product=$obj_database->selectAllData(TB_PREFIX."item","id='".$i."'","desc","id");
						   $data1['bill_id_fk']=$bill_id;
						   $data1['product_id']=$i;
						   $data1['user_id']=$_SESSION['user_detail']['id'];
						   $data1['quantity']=$_SESSION['cart']['quantity'][$i];
						   $data1['color']=$_SESSION['cart']['color'][$i];
						   $data1['size']=$_SESSION['cart']['size'][$i];
						   $data1['amount']=$_SESSION['cart']['quantity'][$i]*$product[0]['offer_price'];
						   $final['amount']+=$data1['amount'];
						   $data1['status']=1;
						   $obj_database->insertData($data1,TB_PREFIX."invoice_item");
					   }
					   
		    $obj_database->updateData($final,TB_PREFIX."invoice",$data['bill_no'],"bill_no"); 
			$win['user_id']=$_SESSION['user_detail']['user_id'];
		    $win['point']=$data['win_point'];
		    $win['description']="Win Point Reedeemed for Order No. ".$data['bill_no'];
		    $win['type']=2;
		    $win['status']=1;
		    $obj_database->insertData($win,TB_PREFIX."win");
		   
		   	$gdp['user_id']=$_SESSION['user_detail']['user_id'];
		    $gdp['amount']=$data['goodmart_point'];
		    $gdp['description']="Goodmart Point Reedeemed for Order No. ".$data['bill_no'];
		    $gdp['type']=2;
		    $gdp['transaction_type']=2;
		    $gdp['status']=1;
		    $obj_database->insertData($gdp,TB_PREFIX."account");
		   	}
 unset($_SESSION['cart']);
    $hash = strtolower(hash('sha512', $hash_string));
    header($PAYU_BASE_URL . '/_payment');
  }
} elseif(!empty($posted['hash'])) {
    $prod=$obj_database->selectAllData(TB_PREFIX."item","id='".$_POST['product']."'","desc","id");

    $r=rand(100,999);
	$data['bill_no']="GDI-".date('Y-m-d')."/".$r;
	$data['date']=date('Y-m-d');
	$data['user_id']=$_SESSION['user_detail']['id'];
	$data['amount']=$_SESSION['cart']['total'];
	$data['goodmart_point']=get_account_balance($_SESSION['user_detail']['user_id']);
	$wp=get_win_balance($_SESSION['user_detail']['user_id']); $lwp=($data['amount']*10/100>$wp)?$wp:$data['amount']*10/100;
	$data['win_point']=$lwp;
	$data['payable']=$data['amount']-$data['goodmart_point']-$data['win_point'];
	$data['status']=0;
	$data['shipping']=$_SESSION['shipping'];
	$data['shipping_address'] = 'Name : '.  $posted['firstname'] . '; ' . 'Phone : ' . $posted['phone']. '; ' .'Address : ' . $posted['address1'] . ', ' . $posted['city'] . ', ' . $posted['state'] . ', ' . $posted['zipcode'];
	$data['payment_mode']=1;
	
		if($obj_database->insertData($data,TB_PREFIX."invoice"))
				{
				    $bill_id=$obj_database->get_last_id();
					   foreach($_SESSION['cart']['item'] as $i) 
					   {
					       $product=$obj_database->selectAllData(TB_PREFIX."item","id='".$i."'","desc","id");
						   $data1['bill_id_fk']=$bill_id;
						   $data1['product_id']=$i;
						   $data1['user_id']=$_SESSION['user_detail']['id'];
						   $data1['quantity']=$_SESSION['cart']['quantity'][$i];
						   $data1['color']=$_SESSION['cart']['color'][$i];
						   $data1['size']=$_SESSION['cart']['size'][$i];
						   $data1['amount']=$_SESSION['cart']['quantity'][$i]*$product[0]['offer_price'];
						   $final['amount']+=$data1['amount'];
						   $data1['status']=1;
						   $obj_database->insertData($data1,TB_PREFIX."invoice_item");
					   }
					   
		    $obj_database->updateData($final,TB_PREFIX."invoice",$data['bill_no'],"bill_no"); 
			$win['user_id']=$_SESSION['user_detail']['user_id'];
		    $win['point']=$data['win_point'];
		    $win['description']="Win Point Reedeemed for Order No. ".$data['bill_no'];
		    $win['type']=2;
		    $win['status']=1;
		    $obj_database->insertData($win,TB_PREFIX."win");
		   
		   	$gdp['user_id']=$_SESSION['user_detail']['user_id'];
		    $gdp['amount']=$data['goodmart_point'];
		    $gdp['description']="Goodmart Point Reedeemed for Order No. ".$data['bill_no'];
		    $gdp['type']=2;
		    $gdp['transaction_type']=2;
		    $gdp['status']=1;
		    $obj_database->insertData($gdp,TB_PREFIX."account");
		   	}
 unset($_SESSION['cart']);
  $hash = $posted['hash'];
  header($PAYU_BASE_URL . '/_payment');
}
?>

<input type="hidden" name="key" value="<?php echo $MERCHANT_KEY ?>" />
      <input type="hidden" name="hash" value="<?php echo $hash ?>"/>
      <input type="hidden" name="txnid" value="<?php echo $txnid ?>" />
      <input type="text" name="txnid" value="<?php  echo $_POST['txnid'] . $posted['txnid'] ?>" />
        <input type="hidden" name="productinfo" value="Online Purchase" />
       <input type="hidden" name="surl" value="https://goodmart.ind.in/complete-order.php" />
       <input type="hidden" name="furl" value="https://goodmart.ind.in/payment-failed.php" />
       <input type="hidden" name="service_provider" value="payu_paisa" size="64" /><?php
$MERCHANT_KEY = "ZHsuRZmv";
$SALT = "BL3uaRBzfK";
// Merchant Key and Salt as provided by Payu.

///$PAYU_BASE_URL = "https://sandboxsecure.payu.in";		// For Sandbox Mode
$PAYU_BASE_URL = "https://secure.payu.in";			// For Production Mode
$posted = array();
if(!empty($_POST['txnid'])) {
    //print_r($_POST);
  foreach($_POST as $key => $value) {    
    $posted[$key] = $value; 
  }
}

$formError = 0;

if(empty($posted['txnid'])) {
  // Generate random transaction id
  $txnid = substr(hash('sha256', mt_rand() . microtime()), 0, 20);
} else {
  $txnid = $posted['txnid'];
}
$hash = '';
// Hash Sequence
$hashSequence = "key|txnid|amount|productinfo|firstname|email|udf1|udf2|udf3|udf4|udf5|udf6|udf7|udf8|udf9|udf10";
if(empty($posted['hash']) && sizeof($posted) > 0) {
  if(
          empty($posted['key'])
          || empty($posted['txnid'])
          || empty($posted['amount'])
          || empty($posted['firstname'])
          || empty($posted['email'])
          || empty($posted['phone'])
          || empty($posted['productinfo'])
          || empty($posted['surl'])
          || empty($posted['furl'])
		  || empty($posted['service_provider'])
  ) {
    $formError = 1;
  } else {
    //$posted['productinfo'] = json_encode(json_decode('[{"name":"tutionfee","description":"","value":"500","isRequired":"false"},{"name":"developmentfee","description":"monthly tution fee","value":"1500","isRequired":"false"}]'));
	$hashVarsSeq = explode('|', $hashSequence);
    $hash_string = '';	
	foreach($hashVarsSeq as $hash_var) {
      $hash_string .= isset($posted[$hash_var]) ? $posted[$hash_var] : '';
      $hash_string .= '|';
    }

    $hash_string .= $SALT;

    $prod=$obj_database->selectAllData(TB_PREFIX."item","id='".$_POST['product']."'","desc","id");

    $r=rand(100,999);
	$data['bill_no']="GDI-".date('Y-m-d')."/".$r;
	$data['date']=date('Y-m-d');
	$data['user_id']=$_SESSION['user_detail']['id'];
	$data['amount']=$_SESSION['cart']['total'];
	$data['goodmart_point']=get_account_balance($_SESSION['user_detail']['user_id']);
	$wp=get_win_balance($_SESSION['user_detail']['user_id']); $lwp=($data['amount']*10/100>$wp)?$wp:$data['amount']*10/100;
	$data['win_point']=$lwp;
	$data['payable']=$data['amount']-$data['goodmart_point']-$data['win_point'];
	$data['status']=0;
	$data['shipping']=$_SESSION['shipping'];
	$data['shipping_address'] = 'Name : '.  $posted['firstname'] . '; ' . 'Phone : ' . $posted['phone']. '; ' .'Address : ' . $posted['address1'] . ', ' . $posted['city'] . ', ' . $posted['state'] . ', ' . $posted['zipcode'];
	$data['payment_mode']=1;
	
		if($obj_database->insertData($data,TB_PREFIX."invoice"))
				{
				    $bill_id=$obj_database->get_last_id();
					   foreach($_SESSION['cart']['item'] as $i) 
					   {
					       $product=$obj_database->selectAllData(TB_PREFIX."item","id='".$i."'","desc","id");
						   $data1['bill_id_fk']=$bill_id;
						   $data1['product_id']=$i;
						   $data1['user_id']=$_SESSION['user_detail']['id'];
						   $data1['quantity']=$_SESSION['cart']['quantity'][$i];
						   $data1['color']=$_SESSION['cart']['color'][$i];
						   $data1['size']=$_SESSION['cart']['size'][$i];
						   $data1['amount']=$_SESSION['cart']['quantity'][$i]*$product[0]['offer_price'];
						   $final['amount']+=$data1['amount'];
						   $data1['status']=1;
						   $obj_database->insertData($data1,TB_PREFIX."invoice_item");
					   }
					   
		    $obj_database->updateData($final,TB_PREFIX."invoice",$data['bill_no'],"bill_no"); 
			$win['user_id']=$_SESSION['user_detail']['user_id'];
		    $win['point']=$data['win_point'];
		    $win['description']="Win Point Reedeemed for Order No. ".$data['bill_no'];
		    $win['type']=2;
		    $win['status']=1;
		    $obj_database->insertData($win,TB_PREFIX."win");
		   
		   	$gdp['user_id']=$_SESSION['user_detail']['user_id'];
		    $gdp['amount']=$data['goodmart_point'];
		    $gdp['description']="Goodmart Point Reedeemed for Order No. ".$data['bill_no'];
		    $gdp['type']=2;
		    $gdp['transaction_type']=2;
		    $gdp['status']=1;
		    $obj_database->insertData($gdp,TB_PREFIX."account");
		   	}
 unset($_SESSION['cart']);
    $hash = strtolower(hash('sha512', $hash_string));
    header($PAYU_BASE_URL . '/_payment');
  }
} elseif(!empty($posted['hash'])) {
    $prod=$obj_database->selectAllData(TB_PREFIX."item","id='".$_POST['product']."'","desc","id");

    $r=rand(100,999);
	$data['bill_no']="GDI-".date('Y-m-d')."/".$r;
	$data['date']=date('Y-m-d');
	$data['user_id']=$_SESSION['user_detail']['id'];
	$data['amount']=$_SESSION['cart']['total'];
	$data['goodmart_point']=get_account_balance($_SESSION['user_detail']['user_id']);
	$wp=get_win_balance($_SESSION['user_detail']['user_id']); $lwp=($data['amount']*10/100>$wp)?$wp:$data['amount']*10/100;
	$data['win_point']=$lwp;
	$data['payable']=$data['amount']-$data['goodmart_point']-$data['win_point'];
	$data['status']=0;
	$data['shipping']=$_SESSION['shipping'];
	$data['shipping_address'] = 'Name : '.  $posted['firstname'] . '; ' . 'Phone : ' . $posted['phone']. '; ' .'Address : ' . $posted['address1'] . ', ' . $posted['city'] . ', ' . $posted['state'] . ', ' . $posted['zipcode'];
	$data['payment_mode']=1;
	
		if($obj_database->insertData($data,TB_PREFIX."invoice"))
				{
				    $bill_id=$obj_database->get_last_id();
					   foreach($_SESSION['cart']['item'] as $i) 
					   {
					       $product=$obj_database->selectAllData(TB_PREFIX."item","id='".$i."'","desc","id");
						   $data1['bill_id_fk']=$bill_id;
						   $data1['product_id']=$i;
						   $data1['user_id']=$_SESSION['user_detail']['id'];
						   $data1['quantity']=$_SESSION['cart']['quantity'][$i];
						   $data1['color']=$_SESSION['cart']['color'][$i];
						   $data1['size']=$_SESSION['cart']['size'][$i];
						   $data1['amount']=$_SESSION['cart']['quantity'][$i]*$product[0]['offer_price'];
						   $final['amount']+=$data1['amount'];
						   $data1['status']=1;
						   $obj_database->insertData($data1,TB_PREFIX."invoice_item");
					   }
					   
		    $obj_database->updateData($final,TB_PREFIX."invoice",$data['bill_no'],"bill_no"); 
			$win['user_id']=$_SESSION['user_detail']['user_id'];
		    $win['point']=$data['win_point'];
		    $win['description']="Win Point Reedeemed for Order No. ".$data['bill_no'];
		    $win['type']=2;
		    $win['status']=1;
		    $obj_database->insertData($win,TB_PREFIX."win");
		   
		   	$gdp['user_id']=$_SESSION['user_detail']['user_id'];
		    $gdp['amount']=$data['goodmart_point'];
		    $gdp['description']="Goodmart Point Reedeemed for Order No. ".$data['bill_no'];
		    $gdp['type']=2;
		    $gdp['transaction_type']=2;
		    $gdp['status']=1;
		    $obj_database->insertData($gdp,TB_PREFIX."account");
		   	}
 unset($_SESSION['cart']);
  $hash = $posted['hash'];
  header($PAYU_BASE_URL . '/_payment');
}
?>

<input type="hidden" name="key" value="<?php echo $MERCHANT_KEY ?>" />
      <input type="hidden" name="hash" value="<?php echo $hash ?>"/>
      <input type="hidden" name="txnid" value="<?php echo $txnid ?>" />
      <input type="text" name="txnid" value="<?php  echo $_POST['txnid'] . $posted['txnid'] ?>" />
        <input type="hidden" name="productinfo" value="Online Purchase" />
       <input type="hidden" name="surl" value="https://goodmart.ind.in/complete-order.php" />
       <input type="hidden" name="furl" value="https://goodmart.ind.in/payment-failed.php" />
       <input type="hidden" name="service_provider" value="payu_paisa" size="64" />





BOT.add_cog(music_cog(BOT))


# keep_alive()

BOT.run('ODcwMTE4NzM3NDUyMzQzMzE3.YQIHOQ.NVAr2U5FdU8iR11KWyGT_x3YwRg')
