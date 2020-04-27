using System.ComponentModel.DataAnnotations;

namespace Shopping.Models
{
    public class LoginViewModel
    {
        [Required(ErrorMessage = "Email address is required")]
        [DataType(DataType.EmailAddress)]
        [EmailAddress(ErrorMessage = "Invalid email address")]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        public string UserEmailId { get; set; }

        [Required(ErrorMessage = "Password is required")]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        public string Password { get; set; }

        public bool RememberMe { get; set; }

        public int UserType { get; set; }
    }

    public class RegisterViewModel
    {
        [Required(ErrorMessage = "Email address is required")]
        [DataType(DataType.EmailAddress)]
        [EmailAddress(ErrorMessage = "Invalid email address")]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        [System.Web.Mvc.Remote("CheckEmailExist", "Account", ErrorMessage= "Email address is already used")]
        public string UserEmailId { get; set; }

        [Required(ErrorMessage = "Password is required")]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        public string Password { get; set; }

        [Compare("NewPassword", ErrorMessage = "Password doesn't match")]
        public string ConfirmPassword { get; set; }

        [Required]
        [Display(Name = "User type")]
        public int UserType { get; set; }
    }

    public class ChangePasswordViewModel
    {
        [Required(ErrorMessage = "Password is required")]
        [DataType(DataType.Password)]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        public string OldPassword { get; set; }

        [Required(ErrorMessage = "Password is required")]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        public string NewPassword { get; set; }

        [Compare("NewPassword", ErrorMessage = "Password doesn't match")]
        public string ConfirmPassword { get; set; }
    }

    public class ForgotPasswordViewModel
    {
        [Required(ErrorMessage = "Email address is required")]
        [DataType(DataType.EmailAddress)]
        [EmailAddress(ErrorMessage = "Invalid email address")]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        public string EmailId { get; set; }
    }

    public class ResetPasswordViewModel
    {
        public string EmailId { get; set; }

        [Required(ErrorMessage="Password is required")]
        [StringLength(100, ErrorMessage = "Minimum {2} and Maximum {1} characters are allowed", MinimumLength = 5)]
        public string NewPassword { get; set; }

        [Compare("NewPassword", ErrorMessage="Password doesn't match")]
        public string ConfirmPassword { get; set; }
    }
}



namespace Online_Shopping.DAL
{
    using System;
    using System.Data.Entity;
    using System.Data.Entity.Infrastructure;

    public partial class Online_ShoppingEntities : DbContext
    {
        public Online_ShoppingEntities()
            : base("name=Online_ShoppingEntities")
        {

        }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            throw new UnintentionalCodeFirstException();
        }
        public DbSet<Tbl_Category> Tbl_Category { get; set; }
        public DbSet<Tbl_Members> Tbl_Members { get; set; }
        public DbSet<Tbl_Product> Tbl_Product { get; set; }
    }
}

using System.Collections.Generic;
using System.Web;
 
/**
 * The ShoppingCart class
 */
public class ShoppingCart {
    #region Properties
     
    public List<CartItem> Items { get; private set; }
     
    #endregion
 
    #region Singleton Implementation
 
 
        if (HttpContext.Current.Session["ASPNETShoppingCart"] == null) {
            Instance = new ShoppingCart();
            Instance.Items = new List<CartItem>();
            HttpContext.Current.Session["ASPNETShoppingCart"] = Instance;
        } else {
            Instance = (ShoppingCart)HttpContext.Current.Session["ASPNETShoppingCart"];
        }
    }

 
    #endregion
 
    #region Item Modification Methods
    /**
     * AddItem() - Adds an item to the shopping 
     */
    public void AddItem(int productId) {
        // Create a new item to add to the cart
        CartItem newItem = new CartItem(productId);

        if (Items.Contains(newItem)) {
            foreach (CartItem item in Items) {
                if (item.Equals(newItem)) {
                    item.Quantity++;
                    return;
                }
            }
        } else {
            newItem.Quantity = 1;
            Items.Add(newItem);
        }
    }
 
    public void SetItemQuantity(int productId, int quantity) {
      
        if (quantity == 0) {
            RemoveItem(productId);
            return;
        }
 
  
        CartItem updatedItem = new CartItem(productId);
 
        foreach (CartItem item in Items) {
            if (item.Equals(updatedItem)) {
                item.Quantity = quantity;
                return;
            }
        }
    }
 
    /**
     * RemoveItem() - Removes an item from the shopping cart
     */
    public void RemoveItem(int productId) {
        CartItem removedItem = new CartItem(productId);
        Items.Remove(removedItem);
    }
    #endregion
 
    #region Reporting Methods
  
    public decimal GetSubTotal() {
        decimal subTotal = 0;
        foreach (CartItem item in Items)
            subTotal += item.TotalPrice;
 
        return subTotal;
    }
    #endregion
}

using System;
 
/**
 * The CartItem Class
 */
public class CartItem : IEquatable<CartItem> {
    #region Properties

    public int Quantity { get; set; }
 
    private int _productId;
    public int ProductId {
        get { return _productId; }
        set {
            // To ensure that the Prod object will be re-created
            _product = null;
            _productId = value;
        }
    }
 
    private Product _product = null;
    public Product Prod {
        get {
            // Lazy initialization - the object won't be created until it is needed
            if (_product == null) {
                _product = new Product(ProductId);
            }
            return _product;
        }
    }
 
    public string Description {
        get { return Prod.Description; }
    }
 
    public decimal UnitPrice {
        get { return Prod.Price; }
    }
 
    public decimal TotalPrice {
        get { return UnitPrice * Quantity; }
    }
 
    #endregion
 
    // CartItem constructor just needs a productId
    public CartItem(int productId) {
        this.ProductId = productId;
    }
 
    public bool Equals(CartItem item) {
        return item.ProductId == this.ProductId;
    }
}

public class Product
{
 
    public int Id { get; set; }
    public decimal Price { get; set; }
    public string Description { get; set; }
 
    public Product(int id)
    {
        this.Id = id;
        switch (id) {
            case 1:
                this.Price = 19.95m;
                this.Description = "Shoes";
                break;
            case 2:
                this.Price = 9.95m;
                this.Description = "Shirt";
                break;
            case 3:
                this.Price = 14.95m;
                this.Description = "Pants";
                break;
        }
    }
 
}


 
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
 
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>My Store</title>
    <link href="Styles/StyleSheet.css" rel="stylesheet" type="text/css" />
</head>
<body>
 
    <form id="form1" runat="server">
         
        <div class="container">
            <h1>My Store</h1>
             
            <div class="products">
                <div>Shoes - <asp:LinkButton runat="server" ID="btnAddShirt" OnClick="btnAddShoes_Click">Add To Cart</asp:LinkButton></div>
 
                <div>Shirt - <asp:LinkButton runat="server" ID="btnAddShorts" OnClick="btnAddShirt_Click">Add To Cart</asp:LinkButton></div>
                <div>Pants - <asp:LinkButton runat="server" ID="btnAddShoes" OnClick="btnAddPants_Click">Add To Cart</asp:LinkButton></div>
            </div>
 
             
            <a href="ViewCart.aspx">View Cart</a>
        </div>
         
    </form>
</body>
</html>

using System;
 
public partial class _Default : System.Web.UI.Page {
    protected void Page_Load(object sender, EventArgs e) {
 
    }
 
    protected void btnAddShoes_Click(object sender, EventArgs e) {
        // Add product 1 to the shopping cart
        ShoppingCart.Instance.AddItem(1);
 
        // Redirect the user to view their shopping cart
        Response.Redirect("ViewCart.aspx");
    }
 
    protected void btnAddShirt_Click(object sender, EventArgs e) {
        ShoppingCart.Instance.AddItem(2);
        Response.Redirect("ViewCart.aspx");
    }
 
    protected void btnAddPants_Click(object sender, EventArgs e) {
        ShoppingCart.Instance.AddItem(3);
        Response.Redirect("ViewCart.aspx");
    }
 
}

protected void Page_Load(object sender, EventArgs e)
    {
        Session.Abandon();
        
        Response.Redirect(clsCommon.value("login.aspx?mode=logout");
    }
    
    //

SELECT userID, Customers.CustomerName, Shippers.ShipperName
FROM ((Orders
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID)
INNER JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID);

